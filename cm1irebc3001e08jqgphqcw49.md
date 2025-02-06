---
title: "End-to-End Kubernetes Observability with ArgoCD, Prometheus, and Grafana on KinD using Terraform"
datePublished: Thu Sep 26 2024 03:53:21 GMT+0000 (Coordinated Universal Time)
cuid: cm1irebc3001e08jqgphqcw49
slug: end-to-end-kubernetes-observability-with-argocd-prometheus-and-grafana-on-kind-using-terraform
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1727321499046/cc8b5fb5-043e-41a0-a2bb-b3225dc5cbd1.png
tags: postgresql, docker, python, kubernetes, monitoring, net, terraform, k8s, helm, prometheus, grafana, kind, argocd, grafana-monitoring, grafana-dashboard

---

This guide will help you set up Prometheus and Grafana on Kubernetes with ArgoCD, ideal for both beginners and experienced DevOps professionals. The project focuses on monitoring Kubernetes clusters using Prometheus (for metrics collection) and Grafana (for visualizing those metrics). We'll also briefly cover ArgoCD for continuous delivery and how to integrate these tools to create a robust observability platform.

## What is KinD?

**KinD (Kubernetes in Docker)**

**Overview:**

**Purpose:** KinD is a tool for running local Kubernetes clusters using Docker container “nodes”.  
**Use Case:** Ideal for testing and developing Kubernetes features in a test environment.

**Advantages:**

* **Simplicity:** Creating and managing a local Kubernetes cluster is straightforward. Ephemeral Nature: Clusters can be easily deleted after testing.
    

**Limitations:**

* **Not for Production:** KinD is not designed for production use.
    
* **Lacks Patching and Upgrades:** No supported way to maintain patching and upgrades.
    
* **Resource Restrictions:** Unable to provide functioning OOM metrics or other resource restriction features.
    
* **Minimal Security:** No additional security configurations are implemented. **Single Machine:** Not intended to span multiple physical machines or be long-lived.
    

**Suitable Use-Cases:**

* **Kubernetes Development:** Developing Kubernetes itself.
    
* **Testing Changes:** Testing application or Kubernetes changes in a continuous integration environment.
    
* **Local Development:** Local Kubernetes cluster application development.
    
* **Cluster API:** Bootstrapping Cluster API.
    

If you would like to learn more about KinD and its use cases, I recommend reading the [KinD project scope](https://kind.sigs.k8s.io/docs/contributing/project-scope/) document which outlines the project’s design and development priorities.

## Prerequisites for This Project

Before starting, make sure you have the following ready:

* [Terraform Code](https://github.com/mrbalraj007/DevOps_free_Bootcamp/tree/main/12.Real-Time-DevOps-Project/Terraform_Code)
    
* [App Code Repo](https://github.com/mrbalraj007/k8s-kind-voting-app)
    
* **Kubernetes Cluster**: A working Kubernetes cluster (KIND cluster or any managed Kubernetes service like EKS or GKE).
    
* **Docker Installed**: To run KIND clusters inside Docker containers.
    
* **Basic Understanding of Kubernetes**: Knowledge of namespaces, pods, and services.
    
* **Kubectl Installed**: For interacting with your Kubernetes cluster.
    
* **ArgoCD Installed**: If you want to use GitOps practices for deploying applications.
    

## Key Steps and Highlights

Here’s what you’ll be doing in this project:

* **Port Forwarding with Prometheus**: Bind Prometheus to port 9090 and forward it to 0.0.0.0:9090. This allows you to access the Prometheus dashboard from your machine.
    
* **Expose Ports on Security Groups**: Open necessary ports (like 9090 for Prometheus and 3000 for Grafana) in your instance's security group to access them from outside.
    
* **Monitoring Services**: Verify that Kubernetes is sending data to Prometheus. You can check this in the Targets section of Prometheus.
    
* **PromQL Queries**: Use PromQL queries to monitor metrics like container CPU usage, network traffic, etc. You can execute these queries to analyze performance metrics.
    
* **Visualizing in Grafana**: After adding Prometheus as a data source in Grafana, create dashboards to visualize CPU, memory, and network usage over time.
    
* **Real-Time Monitoring**: Generate traffic or CPU load in your applications (like the voting app) to observe how Prometheus collects metrics and Grafana displays them.
    
* **Custom Dashboards**: Use pre-built Grafana dashboards by importing them through dashboard IDs, making it easier to visualize complex data without starting from scratch.
    

## Setting Up the Environment

I have created a Terraform file to set up the entire environment, including installing required applications, and tools, and the ArgoCD cluster automatically created.

###### Setting Up the Virtual Machines (EC2)

First, we'll create the necessary virtual machines using `terraform`.

Below is a terraform configuration:

Once you [clone the repo](https://github.com/mrbalraj007/DevOps_free_Bootcamp.git), go to folder *"<mark>12.Real-Time-DevOps-Project/Terraform_Code</mark>"* and run the terraform command.

```bash
cd Terraform_Code/

$ ls -l
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
da---l          25/09/24   7:32 PM                Terraform_Code
```

**Note** ⇒ Make sure to run `main.tf` from inside the folders.

```bash
cd 11.Real-Time-DevOps-Project/Terraform_Code"

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---l          25/09/24   2:56 PM            500 .gitignore
-a---l          25/09/24   7:29 PM           4287 main.tf
```

You need to run `main.tf` the file using the following terraform command.

Now, run the following command.

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply 
# Optional <terraform apply --auto-approve>
```

---

Once you run the terraform command, then we will verify the following things to make sure everything is set up via a terraform.

###### Inspect the `Cloud-Init` logs:

Once connected to the EC2 instance then you can check the status of the `user_data` script by inspecting the log files.

```bash
# Primary log file for cloud-init
sudo tail -f /var/log/cloud-init-output.log
```

* If the user\_data script runs successfully, you will see output logs and any errors encountered during execution.
    
* If there’s an error, this log will provide clues about what failed.
    

###### Verify the Docker version

```bash
ubuntu@ip-172-31-95-197:~$ docker --version
Docker version 24.0.7, build 24.0.7-0ubuntu4.1


docker ps -a
ubuntu@ip-172-31-94-25:~$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS                       NAMES
8436c820f163   kindest/node:v1.31.0   "/usr/local/bin/entr…"   4 minutes ago   Up 3 minutes   127.0.0.1:35147->6443/tcp   kind-control-plane
0d6ed793da0e   kindest/node:v1.31.0   "/usr/local/bin/entr…"   4 minutes ago   Up 3 minutes                               kind-worker
5146cf4dfd20   kindest/node:v1.31.0   "/usr/local/bin/entr…"   4 minutes ago   Up 3 minutes                               kind-worker2
```

###### Verify the KIND cluster:

After Terraform deploys the instance and the cluster is set up, you can SSH into the instance and run:

```bash
kubectl get nodes
kubectl cluster-info
kubectl config get-contexts
kubectl cluster-info --context kind-kind
```

## Managing Docker and Kubernetes Pods

##### Check Docker containers running:

```sh
docker ps
```

```bash
ubuntu@ip-172-31-81-94:~$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS                       NAMES
fb53c1a4ae3d   kindest/node:v1.31.0   "/usr/local/bin/entr…"   5 minutes ago   Up 4 minutes                               kind-worker2
0c9416ad738c   kindest/node:v1.31.0   "/usr/local/bin/entr…"   5 minutes ago   Up 4 minutes                               kind-worker
e2ff2e214404   kindest/node:v1.31.0   "/usr/local/bin/entr…"   5 minutes ago   Up 4 minutes   127.0.0.1:35879->6443/tcp   kind-control-plane
```

##### List all Kubernetes pods in all namespaces:

```sh
kubectl get pods -A
```

* To get the existing namespace
    

```sh
kubectl get namespace
```

```bash
ubuntu@ip-172-31-81-94:~$ kubectl get namespace
NAME                   STATUS   AGE
argocd                 Active   7m11s
default                Active   7m24s
kube-node-lease        Active   7m24s
kube-public            Active   7m24s
kube-system            Active   7m24s
kubernetes-dashboard   Active   7m1s
local-path-storage     Active   7m20s
monitoring             Active   6m54s
```

### Setup Argo CD

##### Verify all nodes in namespace argocd

```bash
kubectl get pods -n argocd 
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321543465/2d23af43-0e8b-4b3a-934f-716b5ef6a124.png align="center")

<details><summary><b><span>Troubleshooting for error CreateContainerConfigError/ImagePullBackOff</span></b></summary><br />as I am getting CreateContainerConfigError/ImagePullBackOff, if you getting the same then follow the below procedure to troubleshoot.<pre><code class="code-line language-bash">kubectl get pods -n argocd
NAME                                                READY   STATUS                       RESTARTS   AGE
argocd-application-controller-0                     0/1     CreateContainerConfigError   0          6m17s
argocd-applicationset-controller-744b76d7fd-b4p4f   1/1     Running                      0          6m17s
argocd-dex-server-5bf5dbc64d-8lp29                  0/1     Init:ImagePullBackOff        0          6m17s
argocd-notifications-controller-84f5bf6896-ztq6q    1/1     Running                      0          6m17s
argocd-redis-74b8999f94-x5cp5                       0/1     Init:ImagePullBackOff        0          6m17s
argocd-repo-server-57f4899557-b4t87                 0/1     CreateContainerConfigError   0          6m17s
argocd-server-7bc7b97977-vh4kg                      0/1     ImagePullBackOff             0          6m17s
</code></pre><p>Below is the steps to futher Troubleshooting<span> </span><strong>Check Image Details</strong>: Run the following command to inspect the pod details:</p><pre><code class="code-line language-bash">kubectl describe pod  -n argocd
</code></pre><p>Specifically, look for the Events section for more details about why the image cannot be pulled.</p><p><strong>Review Logs for More Clues:</strong><span> </span>You can also check the logs of the pods to see if there are more specific error messages that can help pinpoint the issue:</p><pre><code class="code-line language-bash">kubectl logs  -n argocd
</code></pre><p><strong><code>Network or DNS Issues:</code></strong><span> </span>If your EC2 instance does not have proper internet access, it will not be able to pull images from DockerHub or other container registries. Ensure that your EC2 instance has internet access and can reach external container registries.</p><p>You can test connectivity by running:</p><pre><code class="code-line language-bash">curl -I https://registry.hub.docker.com
</code></pre><p>If there are connectivity issues, check the VPC configuration, security groups, and routing tables.</p><p>After researching the issue, it was discovered that there was insufficient disk space on the device.<span> </span><em>Previously, I was utilizing<span> </span><code>8GB</code><span> </span>and edited that Terraform file to make it available<span> </span><code>30GB</code>.</em><span> </span><img src="https://file+.vscode-resource.vscode-cdn.net/c%3A/Users/bsingh/OneDrive%20-%20Jetstar%20Airways%20Pty%20Ltd/Balraj_D_Laptop_Drive/DevOps_Master/DevOps_free_Bootcamp/12.Real-Time-DevOps-Project/image-9.png" alt="alt text" id="image-2" /></p><div data-line="244" class="code-line"></div></details>

##### Check services in Argo CD namespace:

```sh
kubectl get svc -n argocd
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321558196/e686cbd3-817e-4504-821b-f715c4f1b5a1.png align="center")

* Currently, it is set to `clusterIP` and we will change it to `NodePort`
    
* Expose Argo CD server using NodePort:
    

```sh
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321569500/11d4c14b-3628-4dd3-827f-47e636860dd8.png align="center")

##### Forward ports to access Argo CD server:

```sh
kubectl port-forward -n argocd service/argocd-server 8443:443 --address=0.0.0.0 &
# kubectl port-forward -n argocd service/argocd-server 8443:443 &
```

Now, we will open in browser to access argocd. `https://<public IP Address of EC2>: 8443`

[https://18.212.179.217:8443](https://18.212.179.217:8443/)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321583521/c339538f-97df-4261-86e0-f48710be8bfe.png align="center")

* Argo CD Initial Admin Password
    

##### Grab the password to login to the dashboard/Retrieve Argo CD admin password:

```sh
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321605527/f572bc10-3587-4a6b-9e44-17071caecad2.png align="center")

##### Configure the application in argocd

click on &gt; applications&gt;new apps&gt;  
Application Name: voting-app&gt;  
Project Name: default  
Sync Policy: Automatic  
Source&gt;repogitory URL : [https://github.com/mrbalraj007/k8s-kind-voting-app.git](https://github.com/mrbalraj007/k8s-kind-voting-app.git)  
Revision: main  
Path: k8s-specifications # path for your manifest file  
DESTINATION&gt; Cluster URL : [https://kubernetes.default.svc](https://kubernetes.default.svc/)  
Namespace: default

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321630006/9b78448e-7cf5-4d97-8e36-bbf2adcf62c7.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321633924/239a8cdc-d2cd-4b2c-94de-602fd3c8026c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321638374/7d66d708-3dd1-426a-9fcf-c86406385419.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321641370/6b85283c-928f-4483-9cc8-5968c3f50117.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321644875/02addfff-655f-44d9-8e59-649acf65b201.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321649376/237af819-48dd-4819-82fb-ad8d93a48fa5.png align="center")

##### Validate the pods

```sh
kubectl get pods
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321661857/dc4f8cb2-4ceb-47db-9492-07c7b18ac66e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321664956/7ebf1a8b-fe47-4ec5-8b0e-967ea5409692.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321668498/7ff9f189-3a5e-4e1b-8638-2d92cc9504a1.png align="center")

##### Verify the deployment

```sh
kubectl get deployments
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321678770/95a698df-add3-42a8-a5e6-4b56faac38ee.png align="center")

##### Verify the service

```sh
kubectl get svc
```

```bash
kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
db           ClusterIP   10.96.201.0     <none>        5432/TCP         2m12s
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          13m
redis        ClusterIP   10.96.65.124    <none>        6379/TCP         2m12s
result       NodePort    10.96.177.39    <none>        5001:31001/TCP   2m12s
vote         NodePort    10.96.239.136   <none>        5000:31000/TCP   2m12s
```

Now, we will do the port-forward to service `Vote` and `results`

```sh
kubectl port-forward svc/vote 5000:5000 --address=0.0.0.0 &
```

```sh
kubectl port-forward svc/result 5001:5001 --address=0.0.0.0 &
```

##### Verify the `vote` & `results` in browser.

Now, we will open in browser to access services.

`http://<public IP Address of EC2>: 5000` (vote)  
`http://<public IP Address of EC2>: 5001` (result)

For Vote:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321700842/360fc5a3-380a-4a9e-bee0-135b5e914526.png align="center")

For result:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321710554/6ac9e0fe-9397-4dec-b26d-6f0e3b5836c7.png align="center")

Click on any of the items on the voting page to see the results on the result page. I clicked on CATS, and the results are presented below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321724637/b05e322d-7b07-43ac-a402-c8eb3be2ca4c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321734083/6f701ff1-9a27-45f7-8caa-d107bed8096d.png align="center")

**We have successfully set up Argo CD on a Kubernetes cluster & deployed the applications automatically.**

## Setup Kubernetes dashboard:

##### List all Kubernetes pods in all namespaces:

```sh
kubectl get pods -A
```

* To get the existing namespace
    

```sh
kubectl get namespace
```

```bash
ubuntu@ip-172-31-81-94:~$ kubectl get namespace
NAME                   STATUS   AGE
argocd                 Active   18m
default                Active   18m
kube-node-lease        Active   18m
kube-public            Active   18m
kube-system            Active   18m
kubernetes-dashboard   Active   18m
local-path-storage     Active   18m
monitoring             Active   18m
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321753689/678cf55f-3dfa-4e37-a8c9-000261b5bbcc.png align="center")

##### Create a admin user for Kubernetes dashboard: dashboard-adminuser.yml

```bash
sudo tee dashboard-adminuser.yml > /dev/null <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF
kubectl apply -f dashboard-adminuser.yml
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321770756/61b97786-e133-486a-ba4b-27197b378d0c.png align="center")

```bash
ubuntu@ip-172-31-81-94:~$ kubectl get namespace
NAME                   STATUS   AGE
argocd                 Active   23m
default                Active   23m
kube-node-lease        Active   23m
kube-public            Active   23m
kube-system            Active   23m
kubernetes-dashboard   Active   23m
local-path-storage     Active   23m
monitoring             Active   23m
```

##### Create a token for dashboard access:

```sh
kubectl -n kubernetes-dashboard create token admin-user
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321782412/332f9cc2-5a8a-4461-8aa7-f2b62b93a1f0.png align="center")

```bash
token:
eyJhbGciOiJSUzI1NiIsImtpZCI6ImVUMnZfV3dTVFRZVmw4Z2ZUOXRtY0ZiUVVDY1ZYb2R1VTdLaXh3LWxVa2cifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzI3MzIzOTU5LCJpYXQiOjE3MjczMjAzNTksImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwianRpIjoiY2M3NzBhMWUtN2VlNi00Y2UwLTg4YzEtODk2NWUwYjExODc3Iiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiNjRkZGY1YWYtZWU5Ny00MmE2LWE0OTctMTZhYTEzOTBjZThlIn19LCJuYmYiOjE3MjczMjAzNTksInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.UqIZ2GMrrpHF6a820JnXW_WSBXSmmg-B70RvTmUhi2iPXyIF9lvbcZpZ0_FmwcsrRamAtE3ZEzJw_K7_8u4eYTMRftNbiUoIyuFk2Dll-EQC47xYXzlcycdJ6gTbYLDfZecP9-IniroIm3qvo9oAiOBO4ftHr1KYcuKYp21kMMmsC6uxxIQ2iwloqLBuqjZT7oUB3T4ooXQo1lQP_4PwzFaDGPe5XYKmsHCefckpkNVpeSyMYkfvGGWPw8AmvqopRCbEK-GxosSZ1R18zGHzdDYQxEsuCLjPC2A4dJYpqT0q4JaKs6IWjd6yo0WS0sKraYN3Xea8sG3dFE1-djbsHQ
```

Now, we have to port-forward to kubernets dashboard as well.

```sh
kubectl get svc -n kubernetes-dashboard
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321792346/5a0bcfe5-599c-4ad8-8377-3b13954d6e2a.png align="center")

```sh
kubectl port-forward svc/kubernetes-dashboard -n kubernetes-dashboard 8080:443 --address=0.0.0.0 &
```

##### Verify the `dashboard` in browser.

Now, we will open in browser to access services.

`https://<public IP Address of EC2>: 8080`

You have to paste the token you generated above.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321812021/c070a9b5-ebfc-414b-a87a-b98dbc86ed63.png align="center")

Here is the final dashboard view:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321827684/8c9a377f-0afa-4ee2-b511-a6d1c2c34e9e.png align="center")

## Setting Up the Monitoring

### Verify Helm Version

```sh
helm version
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321844476/1918f1e2-0b3f-4da8-8b2f-3d80443974d6.png align="center")

### Verify Prometheus charts in Helm,namespace, and services

```sh
kubectl get svc -n monitoring
kubectl get namespace
```

##### To view all pods in the monitoring namespace.

```sh
kubectl get pods -n monitoring
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321854857/3073020d-31e4-43d3-ae50-9890d9d6052f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321861718/85ff8cee-09b1-45c6-ae93-f621cb87107d.png align="center")

Over all Pods details

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321870725/020b0199-b2e7-4a71-8b3b-77b09f6a0d4a.png align="center")

Now, we have to port-forward for `kind-prometheus-kube-prome-prometheus` & `kind-prometheus-grafana`

```sh
kubectl get svc -n monitoring
```

```bash
 kubectl get svc -n monitoring
NAME                                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                         AGE
alertmanager-operated                      ClusterIP   None           <none>        9093/TCP,9094/TCP,9094/UDP      6m17s
kind-prometheus-grafana                    NodePort    10.96.184.88   <none>        80:31000/TCP                    6m23s
kind-prometheus-kube-prome-alertmanager    NodePort    10.96.44.175   <none>        9093:32000/TCP,8080:31594/TCP   6m23s
kind-prometheus-kube-prome-operator        ClusterIP   10.96.88.60    <none>        443/TCP                         6m23s
kind-prometheus-kube-prome-prometheus      NodePort    10.96.47.99    <none>        9090:30000/TCP,8080:32323/TCP   6m23s
kind-prometheus-kube-state-metrics         ClusterIP   10.96.120.53   <none>        8080/TCP                        6m23s
kind-prometheus-prometheus-node-exporter   NodePort    10.96.34.125   <none>        9100:32001/TCP                  6m23s
prometheus-operated                        ClusterIP   None           <none>        9090/TCP                        6m15s
```

For Prometheus

```sh
kubectl port-forward svc/kind-prometheus-kube-prome-prometheus -n monitoring 9090:9090 --address=0.0.0.0 & 
```

##### Verify the `prometheus` in browser.  

Now, we will open the browser to access services.  
`http://<public IP Address of EC2>:9090`  
`http://<public IP Address of EC2>:9090/metrics` # It is sent to Prometheus [http://18.206.155.5:9090/metrics](http://18.206.155.5:9090/metrics)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321910678/abc12157-fe89-4915-a661-26ffb159743c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321915443/366fab40-c556-4a91-9186-319ac6c8aa60.png align="center")

##### Prometheus Queries

paste the below query in expression on Prometheus and click on execute

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321927849/43479db2-bd4a-4f94-9339-346508631905.png align="center")

```sh
sum (rate (container_cpu_usage_seconds_total{namespace="default"}[1m])) / sum (machine_cpu_cores) * 100
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321946607/8ba7a540-d0f4-4417-88e8-86de5a4e796f.png align="center")

Now, click on the graph after executing the command

For network query-

```sh
sum(rate(container_network_receive_bytes_total{namespace="default"}[5m])) by (pod)
sum(rate(container_network_transmit_bytes_total{namespace="default"}[5m])) by (pod)
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727321994578/192433c7-adb6-4d93-b87a-c41eb054531e.png align="center")

Now, we will use our vote service to see the traffic in prometheous.

```sh
kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
db           ClusterIP   10.96.201.0     <none>        5432/TCP         68m
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          79m
redis        ClusterIP   10.96.65.124    <none>        6379/TCP         68m
result       NodePort    10.96.177.39    <none>        5001:31001/TCP   68m
vote         NodePort    10.96.239.136   <none>        5000:31002/TCP   68m
```

Exposing the `Vote`app

```sh
kubectl port-forward svc/vote 5000:5000 --address=0.0.0.0 & 
```

If it is already exposed then don't need to perform.

### Configure the Grafana

```sh
kubectl get svc -n monitoring
```

```bash
NAME                                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                         AGE
alertmanager-operated                      ClusterIP   None           <none>        9093/TCP,9094/TCP,9094/UDP      33m
kind-prometheus-grafana                    NodePort    10.96.184.88   <none>        80:31000/TCP                    33m
kind-prometheus-kube-prome-alertmanager    NodePort    10.96.44.175   <none>        9093:32000/TCP,8080:31594/TCP   33m
kind-prometheus-kube-prome-operator        ClusterIP   10.96.88.60    <none>        443/TCP                         33m
kind-prometheus-kube-prome-prometheus      NodePort    10.96.47.99    <none>        9090:30000/TCP,8080:32323/TCP   33m
kind-prometheus-kube-state-metrics         ClusterIP   10.96.120.53   <none>        8080/TCP                        33m
kind-prometheus-prometheus-node-exporter   NodePort    10.96.34.125   <none>        9100:32001/TCP                  33m
prometheus-operated                        ClusterIP   None           <none>        9090/TCP                        33m
```

For grafana (expose port)

```bash
kubectl port-forward svc/kind-prometheus-grafana -n monitoring 3000:80 --address=0.0.0.0 &
```

##### Verify the `Grafana` in browser.

Now, we will open the browser to access services.  
`http://<public IP Address of EC2>:3000`  
[http://18.206.155.5:3000](http://18.206.155.5:3000/)  
username: admin  
Password: prom-operator

**Note**\--&gt; In Grafana, we have to add data sources and create a dashboard.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322031271/bef2b844-e9d2-4bf8-90f2-bb9595a63a99.png align="center")

##### Creating a user in Grafana

* Now, we will create a test user and will give permission accordingly.  
    Home&gt; Administration&gt; Users and access&gt; Users&gt; create user
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322049003/e502f00d-32bd-4620-bc3a-8a7bbf4c6904.png align="center")
    
    You can change the role as well, as per the below screenshot.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322059367/2b9eeb06-6e73-459b-95dc-14329107a44e.png align="center")
    
    ##### Creating a dashboard in Grafana  
    
    Home&gt; Connections&gt; Data sources &gt;Build a dashboard&gt;add visualization&gt; Select the Prometheus
    
    select the matrix, and label filter... Hit on run query&gt;click save.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322164810/559dd657-5e65-4ca6-b053-3051a1aec77f.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322169602/cd92ebe2-b92f-448b-a211-185f17ee1d05.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322173929/88359d29-b9d4-4fb7-9e39-82ccf7fcb2ea.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322178273/188abb2a-c0b6-442c-8090-ce782c4a59f2.png align="center")
    
    If you want to use the custom dashboard [then do-](https://grafana.com/grafana/dashboards/15661-1-k8s-for-prometheus-dashboard-20211010/) open Google and reach "`grafana dashboard`"I choose this [dashboard](https://grafana.com/grafana/dashboards/15661-1-k8s-for-prometheus-dashboard-20211010/) Click on `Copy ID to clickboard`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322189471/f1168d49-3434-43cd-9be3-b204d44cd320.png align="center")
    
    Now, go to the Grafana dashboard. click on new&gt; select Import&gt; type the dashboard ID "15661" and click on load&gt; select the Prometheus as a data source and click on import.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322214968/8cf6e6fc-eaf7-4212-8563-ae882a2d244f.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322221073/b1c29d76-4782-4d03-b47a-c19eebc5d611.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322225864/c4a98bed-94e5-4233-8f36-eb7f5b501096.png align="center")
    
    Final Dashboard
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322236371/baec5b09-9384-4d47-9d8e-84a1e63180fd.png align="center")
    
    AWS Resources:
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727322279445/27552f67-e14b-4990-81dc-c4d288dfe155.png align="center")
    
    ## Environment Cleanup:
    
    ##### Delete ArgoCD
    
    To remove ArgoCD resources, it is recommended to delete the corresponding argocd namespace within the Kubernetes environment. This can be achieved by executing the following command:
    
    ```bash
    kubectl delete namespace argocd
    ```
    
    ##### Delete KinD Cluster
    
    Once you have completed your testing, you may delete the KinD cluster by executing the following command, which will remove the associated Docker containers and volumes.
    
    ```bash
    kind delete cluster --name argocd
    ```
    
    *Note*⇒:It is critical that the name of the cluster to be destroyed be supplied. In the current demonstration, a cluster with the name argocd was established, hence the same name must be supplied during the deletion procedure. Failure to supply the cluster name would result in the deletion of the default cluster, even though it does not exist. It is crucial to note that in such a scenario, no error notice will be provided, and the prompt will state that the cluster was successfully deleted. To minimize any potential interruption to test pipelines, we have conducted conversations with the KinD maintainers team and agreed that it is critical to mention the actual name of the cluster when deletion.
    
    ##### Time to delete the Virtual machine.
    
* <mark>Note- </mark> As we are using Terraform, we will use the following command to delete the environment
    
    Go to folder *"<mark>12.Real-Time-DevOps-Project/Terraform_Code</mark>"* and run the Terraform command.
    
    ```bash
    cd Terraform_Code/
    
    $ ls -l
    Mode                 LastWriteTime         Length Name
    ----                 -------------         ------ ----
    da---l          25/09/24   9:48 PM                Terraform_Code
    
    Terraform destroy --auto-approve
    ```
    
    ## Key Takeaways
    
    * **Prometheus for Metrics Collection**: It's a time-series database that scrapes data from Kubernetes services.
        
    * **Grafana for Visualization**: Allows you to create beautiful dashboards from the metrics data stored in Prometheus.
        
    * **Real-Time Monitoring**: You can observe real-time performance metrics for CPU, memory, and network using the dashboards.
        
    * **GitOps with ArgoCD**: ArgoCD makes deploying and managing applications on Kubernetes easier by automating continuous deployment.
        
    
    ## What to Avoid
    
    * **Incorrect Port Forwarding**: Make sure you expose the correct ports for both Prometheus (9090) and Grafana (3000). Missing this can lead to connection issues.
        
    * **Skipping Security Groups Configuration**: Always open the necessary ports in your cloud provider's security groups. Otherwise, the services won't be accessible externally.
        
    * **Not Using PromQL Effectively**: PromQL can seem complex, but it's essential to retrieve valuable metrics like CPU or memory usage. Avoid using generic queries without understanding the context of your services.
        
    
    ## Benefits of Using This Setup
    
    * **Improved Observability**: Combining Prometheus and Grafana gives you full observability over your Kubernetes clusters.
        
    * **Real-Time Metrics**: You can monitor your services in real-time, making troubleshooting faster and more efficient.
        
    * **Scalability**: This setup grows with your infrastructure, allowing you to monitor large-scale deployments with ease.
        
    * **Cost-Efficient**: Both Prometheus and Grafana are open-source, providing a cost-effective monitoring solution.
        
    * **Ease of Use**: With Grafana's pre-built dashboards and PromQL's flexibility, it’s easy to get started and scale monitoring solutions.
        
    
    **Ref Link**
    
    * [Kubernetes With ArgoCD](https://www.youtube.com/watch?v=Kbvch_swZWA&list=PLJcpyd04zn7rZtWrpoLrnzuDZ2zjmsMjz&index=122)
        
    * [Prometheus & Grafana Tutorial](https://www.youtube.com/watch?v=DXZUunEeHqM&list=PLJcpyd04zn7rZtWrpoLrnzuDZ2zjmsMjz&index=122)
        
    * [Kind](https://github.com/kubernetes-sigs/kind?tab=readme-ov-file)
        
    * [Kind Documentation](https://kind.sigs.k8s.io/)
        
    * [Kind Release version](https://github.com/kubernetes-sigs/kind/releases)