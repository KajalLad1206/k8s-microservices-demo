# 🛍️ Microservices Demo — Kubernetes Deployment

This repository contains Kubernetes manifests for deploying the **Google Microservices Demo** application.  
Each service runs in its own Deployment and is exposed internally using a ClusterIP Service.  
The `frontend` service is exposed externally using a LoadBalancer or NodePort.

---
![Alt text](architecture-diagram.png)

---

## 📦 Services Overview

### **1. emailservice**
- Handles outbound emails (mocked).
- gRPC-based health checks.
- Container Port: `8080`
- Service Port: `5000`

### **2. recommendationservice**
- Provides recommended products.
- Depends on: `productcatalogservice`.
- Port: `8080`

### **3. paymentservice**
- Processes simulated payments.
- Port: `50051`

### **4. productcatalogservice**
- Provides product catalog data.
- Port: `3550`

### **5. currencyservice**
- Performs currency conversions.
- Port: `7000`

### **6. shippingservice**
- Calculates shipping fees and times.
- Port: `50051`

### **7. adservice**
- Provides ads and promotions.
- Port: `9555`

### **8. cartservice**
- Manages user shopping carts.
- Depends on: `redis-cart`
- Port: `7070`

### **9. redis-cart**
- Redis in-memory store for cartservice.
- Uses `emptyDir` for temporary storage.
- Port: `6379`

### **10. checkoutservice**
- Orchestrates checkout workflow.
- Depends on:
  - `productcatalogservice`
  - `shippingservice`
  - `paymentservice`
  - `emailservice`
  - `currencyservice`
  - `cartservice`
- Port: `5050`

### **11. frontend**
- Main web UI of the application.
- Depends on various backend services.
- Container Port: `8080`
- Exposed via:
  - `LoadBalancer` (external)
  - `NodePort` (optional)

---

## 🚀 Deployment Instructions

### **1. Apply all manifests**
```sh
kubectl apply -f <filename>.yaml
kubectl apply -f config.yaml
