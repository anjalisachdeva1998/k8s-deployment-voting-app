# Simple Kubernetes Pod-Based Deployment for a Sample Distributed App

This repository contains a **simple Kubernetes (K8s) setup** for a sample distributed application. The project focuses on running a multi-component application on Kubernetes while intentionally keeping the resource model minimal and straightforward.

---

## 🎯 Project Objective

* Experiment with a minimal Kubernetes setup for a distributed application
* Understand how Pods communicate using Services
* Explore service discovery and networking within a Kubernetes cluster

---

## 🏗️ Application Architecture

The application consists of multiple independent components that communicate with each other via internal services.

### Components

1. **Vote App**

   * Frontend application where users cast votes
   * Writes vote data to Redis

2. **Redis**

   * In-memory data store
   * Temporarily stores voting data

3. **Worker**

   * Background service
   * Reads vote data from Redis
   * Writes processed data to PostgreSQL

4. **PostgreSQL DB**

   * Persistent database
   * Stores final voting results

5. **Result App**

   * Frontend application
   * Reads results from PostgreSQL and displays them

---

## ☸️ Kubernetes Resources Created

### 1️⃣ Pods

A separate Pod is created for each component of the application:

* Vote App Pod
* Redis Pod
* Worker Pod
* PostgreSQL Pod
* Result App Pod

### 2️⃣ Services

Services are used to enable communication between Pods and to expose selected components internally and some externally.

#### 🔹 ClusterIP Services (Internal Communication)

These services are accessible **only within the Kubernetes cluster**:

* **Redis Service**

  * Used by:

    * Vote App (write operations)
    * Worker (read operations)

* **Db (PostgreSQL) Service**

  * Used by:

    * Worker (write operations)
    * Result App (read operations)

#### 🔹 NodePort Services (External Access)

These services expose applications outside the cluster for user access:

* **Vote App Service**

  * Allows users to access the voting interface

* **Result App Service**

  * Allows users to view voting results

---

## 🚀 How to Use This Repository

1. Ensure you have a working Kubernetes cluster (Minikube, Kind, or any local cluster)
2. Run the following command in the folder where the files are present

   ```
   kubectl apply -f .
   ```
   Verification

   ```
   k8s-deployment-votingapp (git)% kubectl get all
    NAME         READY   STATUS    RESTARTS   AGE
    pod/db       1/1     Running   0          15h
    pod/redis    1/1     Running   0          15h
    pod/result   1/1     Running   0          15h
    pod/vote     1/1     Running   0          15h
    pod/worker   1/1     Running   0          15h
    
    NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    service/db           ClusterIP   10.x.x.x   <none>        5432/TCP         15h
    service/kubernetes   ClusterIP   10.x.x.x       <none>        443/TCP          15h
    service/redis        ClusterIP   10.x.x.x   <none>        6379/TCP         15h
    service/result       NodePort    10.x.x.x    <none>        8080:31001/TCP   15h
    service/vote         NodePort    10.x.x.x     <none>        8080:31000/TCP   15h
   ```
4. Retreive the node IP and access Access the Vote App and Result App using the assigned NodePorts:

   ```
   k8s-deployment-votingapp (git)% kubectl get node -o wide
    NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE            KERNEL-VERSION   CONTAINER-RUNTIME
    minikube   Ready    control-plane   15h   v1.34.0   a.b.c.d     <none>        Buildroot 2025.02   6.6.95           docker://28.4.0
   ```
   Note: IP masked here on purpose
   ```
   enter this in browser: a.b.c.d:31000 to view the vote app and a.b.c.d:31001 to view the result app
   ```
