# Kubernetes on AWS - EKS cluster with AWS Management Console

### Demo Project:
Create AWS EKS cluster with a Node Group and Deploy Sample Application

### Technologies Used:
Kubernetes, AWS EKS

### Project Description:
- Configure necessary IAM Roles
- Create VPC with Cloudformation Template for Worker Nodes
- Create EKS cluster (Control Plane Nodes)
- Create Node Group for Worker Nodes and attach to EKS cluster
- Configure Auto-Scaling of worker nodes
- Deploy a sample application to EKS cluster
---
### Steps to create an EKS cluster
1. create EKS IAM Role
2. create VPC for Worker Nodes
3. create EKS cluster (Master Nodes)
4. connect kubectl with EKS cluster
5. create EC2 IAM Role for Node Group
6. create Node Group and attach to EKS cluster
7. configure auto-scaling
8. deploy our application to our EKS cluster

### Configure IAM Roles
1. create IAM Role in our AWS account
    - role will be assigned to EKS cluster managed by AWS
    - will allow AWS to create and manage components on our behalf
    - in IAM console, go to Roles, select 'Create role'
        - select 'AWS Service'
        - select 'EKS'
        - select 'EKS Cluster'
        - click 'Next' until the review page and enter a name
        - 'Create role'

* Kubelet on Worker Node (EC2 instance) needs permission
1. create EC2 IAM Role
    - IAM > Roles > Create role
    - select 'AWS Service'
    - select 'EC2'
    - add permissions AmazonEKSWorkerNodePolicy, AmazonEC2ContainerRegistryReadOnly, AmazonEKS_CNI_Policy
    - 'Create role'

### Create VPC for Worker Nodes
* Why create another VPC?
    - EKS cluster needs specific networking configuration
    - K8s specific and AWS specific networking rules
    - Default VPC is not optimized for it
* Worker Nodes need specific Firewall configurations
    - For Master <--> Worker Node communication
    - Best practice: configure Public Subnet (Cloud Native LoadBalancer - Elastic Load Balancer) and Private Subnet (LoadBalancer Service)
* With the IAM Role, Kubernetes has permission to change VPC configurations

1. instead of creating the VPC yourself, use a template from CloudFormation
    - go to CloudFormation in the console
    - 'Create stack' 
    - select 'Template is ready'
    - select 'Amazon S3 URL'
    - copy and paste the URL for the template from: https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html
    - use this url for public private network : https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/amazon-eks-vpc-private-subnets.yaml
    - 'Next' then enter a name : eks-vpc-worker-node-stack-VPC
    - leave everything else default

### Create EKS cluster
1. 'Add cluster'
    - enter a name : eks-cluster-test
    - leave 'Kubernetes version' default
    - select the created IAM role for the cluster in 'Cluster service role'
    - select the created Node IAM role for the EC2 in 'Cluster'
    - 'Next'
    - for 'VPC', select the created VPC
    - for 'Security groups', select the SGs tied to the created VPC
    - select 'Public and private' for 'Cluster endpoint access'

### Connect kubectl 
1. Create kubeconfig file
    - `aws eks update-kubeconfig --name eks-cluster-test`
2. useful commands
    - kubectl get nodes 
    - kubectl get ns
    - kubectl cluster-info

### Create Node Group for Worker Nodes
* Worker Nodes run Worker Processes
* Kubelet is the main process - scheduling, managing Pods and communicate with other AWS Services
1. add node group to eks cluster
    - go to the EKS cluster, then to 'Compute' tab
    - under 'Node Groups', select 'Add node group'
    - enter a name and add the created node group role
    - on the next page, select EC2 instance type
        - select 'Amazon Linux' AMI
        - t3.medium instance type
    - on the next page, enable SSH access to nodes
        - select a KeyPair
        - 'Allow SSH Access from All'
    - 'Create'
2. execute `kubectl get nodes` to see the running EC2 instances

### Configure Auto-Scaling
1. in the 'Details' section of the 'Node Group Configuration' page
    - will see 'Autoscaling group name'
2. select the autoscaling group
    - Autoscaling group logically groups EC2 instances together and has a configuration with minimum number of instances to scale down to and maximum number of instances to scale up to
    - AWS does not automatically autoscale resources 
    - have to configure K8s Autoscaler, this will work together with the autoscaling group
3. create autoscaling group (just use the default one that is created)
4. create a role for EC2 instances
    - create a custom policy
    ```
    // node-group-autoscale-policy
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "autoscaling:DescribeAutoScalingGroups",
                    "autoscaling:DescribeAutoScalingInstances",
                    "autoscaling:DescribeLaunchConfigurations",
                    "autoscaling:DescribeTags",
                    "autoscaling:SetDesiredCapacity",
                    "autoscaling:TerminateInstanceInAutoScalingGroup",
                    "ec2:DescribeLaunchTemplateVersions"
                ],
                "Resource": "*",
                "Effect": "Allow"
            }
        ]
    }
    ```
    - attach the policy to existing node group IAM Role
5. apply autoscaler discovery yaml file
    - `kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml`
    - check the cluster: `kubectl get deployment -n kube-system cluster-autoscaler`
6. update the deployment configuration
    - `kubectl edit deployment -n kube-system cluster-autoscaler`
    - in 'annotations', add: cluster-autoscaler.kubernetes.io/safe-to-evict: "false"
    - replace "YOUR CLUSTER NAME" with the actual eks cluster name (eks-cluster-test)
    - under command add: --balance-similar-node-groups and --skip-nodes-with-system-pods=false
    - update image version to the kubernetes version used by the cluster
        - use https://github.com/kubernetes/autoscaler/tags to find the version that matches with the kubernetes version on the cluster
7. can also just download the cluster-autoscaler-autodiscover.yaml and edit it with the above changes and re-apply
8. check on the pod: `kubectl get pods -n kube-system`

### Deploy Sample Application
1. sample nginx deployment config: https://github.com/KajalLad1206/k8s-microservices-demo/blob/main/eks-cluster/nginx-config.yaml
    - internal service with AWS loadbalancer attached to it
2. apply the nginx config: `kubectl apply -f nginx.yaml`
3. see the cluster and external ips of the service
    - `kubectl get svc`
4. a loadbalancer will get created in AWS
    - the dns will match the external ip seen for the service
    - use that dns to access the ui from the browser
---
---
### Clean Up Cluster Resources
1. delete Node Groups 
2. delete the cluster
3. delete the created eks roles
4. delete the cloudformation stack 
---
---
### Demo Project:
Create EKS cluster with eksctl

### Technologies Used:
Kubernetes, AWS EKS, Eksctl, Linux

### Project Description:
- Create EKS cluster using eksctl tool that reduces the manual effort of creating an EKS cluster
---
### Create EKS cluster
- eksctl overview
    - command line tool for working with EKS clusters that automates many individual tasks
    - execute just one command
    - necessary components gets created and configured in the background
    - cluster will be created with default parameters
    - with more cli options you can customize your cluster
    - https://eksctl.io/

1. install eksctl
    - https://github.com/weaveworks/eksctl#installation
2. make aws credentials are configured
3. create eks cluster command
    ```
        # --version is kubernetes version

        eksctl create cluster
            --name demo-cluster
            --version 1.34
            --region us-east-1
            --nodegroup-name demo-nodes
            --node-type t2.medium
            --nodes 2
            --nodes-min 1
            --nodes-max 3
    ```
4. can also create a config file (similar to k8s yaml format) to pass to the eksctl command to create a cluster
    `eksctl create cluster -f cluster.yaml`