# **Mini Project: Introduction to GitOps and ArgoCD using AWS**

## **Project Overview**

This project introduces learners to the principles of **GitOps** and its implementation using **ArgoCD** on **Amazon EKS (Elastic Kubernetes Service)**. It focuses on deploying applications declaratively, ensuring consistency between Git repositories (desired state) and Kubernetes clusters (live state).

Learners will gain hands-on experience in setting up ArgoCD, exploring its architecture, and managing deployments using GitOps workflows.

## **Why is this Project Relevant?**

As organizations move toward cloud-native technologies, the need for **automated, reliable, and scalable deployment strategies** is increasing. GitOps with ArgoCD provides a powerful solution by:

* Enforcing **Infrastructure as Code (IaC)**.
* Ensuring **self-healing deployments**.
* Offering **traceable change management**.
* Managing **multi-environment and multi-cluster deployments** with ease.

This project equips learners with essential **DevOps and cloud-native skills** that are highly in demand across the tech industry.

## **Project Goals and Objectives**

* Understand GitOps principles and benefits.
* Install and configure ArgoCD in an AWS-hosted Kubernetes environment (Amazon EKS).
* Explore ArgoCD’s architecture and key components.
* Deploy and synchronize applications using GitOps workflows.
* Gain practical experience with cloud-native DevOps best practices.

## **Prerequisites**

1. Basic understanding of Kubernetes (pods, deployments, services).
2. An active AWS account.
3. Running Amazon EKS cluster.
4. Installed tools:

   * **kubectl**
   * **AWS CLI**
   * **Git**
   * **Text Editor/IDE (e.g., VSCode)**
5. Optional but recommended:

   * **Docker**
   * **Helm**
   * **Kustomize**


## **Project Deliverables**

* A GitOps-enabled **EKS cluster** with ArgoCD installed.
* Documentation and architecture explanation.
* Sample application deployed using ArgoCD.
* Step-by-step project README.md with screenshots and commands.

## **Tools & Technologies Used**

* **AWS (Amazon Web Services)** – for hosting the Kubernetes cluster (EKS).
* **Kubernetes** – container orchestration.
* **ArgoCD** – GitOps continuous delivery tool.
* **Git** – version control system.
* **kubectl** – Kubernetes command-line tool.
* **AWS CLI** – AWS resource management.
* **VSCode** – code editor.
* **Docker** (optional).
* **Helm & Kustomize** (optional).

## **Project Components**

* **Amazon EKS Cluster** (Kubernetes environment).
* **ArgoCD** (GitOps operator).
* **Git Repository** (source of truth for deployments).
* **Sample Application** (to demonstrate synchronization).
* **Documentation (README.md)**.

## **Task 1: Project Directory and Structure**

The first step is to create the main project directory with all the necessary sub-directories and files. This ensures we maintain a clean and organized workflow throughout the project.

### **Step 1: Commands to Create Project Structure**

Run the following commands in your terminal:

```bash
# Create main project directory
mkdir gitops-argocd-aws && cd gitops-argocd-aws

# Create sub-directories
mkdir -p manifests/app manifests/argocd
mkdir images

# Create README.md file
touch README.md
```

### **Step 2: Explanation of Structure**

* **README.md** → This will serve as the main documentation file containing all steps, explanations, and usage instructions.
* **manifests/** → This folder will hold all Kubernetes YAML manifests.

  * **app/** → Contains manifests for deploying a sample application.
  * **argocd/** → Contains manifests related to ArgoCD installation and configuration.
* **images/** → Used to store screenshots and diagrams that will be referenced in the README.

### **Step 3: Directory Structure**

```bash
gitops-argocd-aws/
├── README.md                # Main project documentation
├── manifests/               # Kubernetes manifests
│   ├── app/                 # Sample application manifests
│   └── argocd/              # ArgoCD installation & config manifests
└── images/                  # Screenshots and diagrams
```

### **Task 1 Completion Note**

✔️ Task 1 Completed – Project directory and sub-directory structure are successfully created and organized.

## **Task 2: Installing and Configuring ArgoCD on Amazon EKS**

This task sets up ArgoCD inside your Amazon EKS cluster. ArgoCD will serve as the GitOps operator, synchronizing Kubernetes manifests stored in Git with the live cluster.

### **Step 1: Set Up eksctl on Windows**

#### 1. Install `eksctl`

1. Download the latest Windows release: 👉 [eksctl releases](https://github.com/eksctl-io/eksctl/releases/latest)
2. Extract **`eksctl.exe`**.
3. Move it to a directory in your **PATH** (e.g., `C:\Program Files\eksctl\`).
4. Add the path to **System Environment Variables → Path**.
5. Verify installation:

   ```powershell
   eksctl version
   ```

#### 2. Install `kubectl`

1. Download from official docs: 👉 [Install kubectl on Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)
2. Add it to PATH.
3. Verify:

   ```powershell
   kubectl version --client
   ```

#### 3. Install AWS CLI

1. Download: 👉 [AWS CLI installer](https://aws.amazon.com/cli/)
2. Install and verify:

   ```powershell
   aws --version
   ```
3. Configure credentials:

   ```powershell
   aws configure
   ```

   Enter:

   * AWS Access Key ID
   * AWS Secret Access Key
   * Default region (e.g., `us-east-1`)
   * Output format (json)

**Screenshot:** aws configure
![aws configure](./images/1.aws_configure.png)

#### 4. Create an EKS Cluster

Example cluster creation command:

```powershell
eksctl create cluster \
  --name gitops-eks-cluster \
  --region us-east-1 \
  --nodes 2 \
  --node-type t3.medium \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed
```

📌 This takes \~10–15 minutes.

Verify cluster:

```powershell
kubectl get nodes
```

**Screenshot:** kubectl get nodes
![kubectl get nodes](./images/2.kubectl_get_nodes.png)

### **Step 2: Create Namespace for ArgoCD**

Run:

```powershell
kubectl create namespace argocd
```

**Screenshot:** kubectl create namespace argocd
![kubectl create namespace argocd](./images/3.kubectl_create_namespace_argocd.png)

### **Step 3: Install ArgoCD**

Apply the official installation manifests:

```powershell
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### **Step 4: Verify Installation**

Check pods:

```powershell
kubectl get pods -n argocd
```

**Screenshot:** kubectl get pods -n argocd
![kubectl get pods -n argocd](./images/4.kubectl_get_pods_n_argocd.png)


### **Step 5: Access ArgoCD UI**

#### 1. Port Forward (local access)

```powershell
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

**Screenshot:** kubectl port-forward
![kubectl port-forward svc/argocd-server -n argocd 8080:443](./images/5.kubectl_port-forward_svc_argocd.png)

Open in browser: 👉 [https://localhost:8080](https://localhost:8080)

**Screenshot:** Localhost
![Localhost](./images/6.localhost.png)

#### 2: Expose via LoadBalancer (cloud access)

Create `manifests/argocd/argocd-server-lb.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: argocd-server-lb
  namespace: argocd
spec:
  type: LoadBalancer
  ports:
    - name: https
      port: 443
      targetPort: 8080
  selector:
    app.kubernetes.io/name: argocd-server
```

Apply it:

```powershell
kubectl apply -f manifests/argocd/argocd-server-lb.yaml
```

**Screenshot:** Kubectl apply manifests
![Kubectl apply manifests](./images/7.kubectl_apply_manifests.png)

Get external IP:

```powershell
kubectl get svc argocd-server-lb -n argocd
```

**Screenshot:** kubectl get svc argocd-server-lb -n argocd
![kubectl get svc argocd-server-lb -n argocd](./images/9.kubectl_get_svc_argocd.png)

Access in browser:
👉 `https://abd7a5d81c34445e1a1e1ba9cfbdbe82-973115457.us-east-1.elb.amazonaws.com`

### **Step 6: Login to ArgoCD**

Retrieve the initial admin password:

```powershell
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d; echo
```

Login via CLI:

```powershell
argocd login abd7a5d81c34445e1a1e1ba9cfbdbe82-973115457.us-east-1.elb.amazonaws.com
```

Login via UI:

* Username: `admin`
* Password: (retrieved above)

**Screenshot:** admin password
![admin password](./images/10.argocd_password.png)


### **Task 2 Completion Note**

✔️ Task 2 Completed – ArgoCD is successfully installed and accessible via UI and CLI on the EKS cluster.

## **Task 3: Deploying a Sample Application with ArgoCD**

This task demonstrates how to deploy a Kubernetes application using ArgoCD. The purpose is to show GitOps in action, where the desired state of an application is stored in Git and automatically synchronized to your EKS cluster.

### **Step 1: Project Directory Structure**

After Task 3 additions, your project directory should look like this:

```bash
gitops-argocd-aws/
├── README.md                # Main project documentation
├── manifests/               # Kubernetes manifests
│   ├── app/                 # Sample application manifests
│   │   ├── nginx-deployment.yaml
│   │   └── nginx-service.yaml
│   └── argocd/              # ArgoCD installation & config manifests
│       └── nginx-app.yaml   # ArgoCD Application manifest for Nginx
└── images/                  # Screenshots and diagrams
    ├── 1.aws_configure.png
    ├── 2.kubectl_get_nodes.png
    ├── 3.kubectl_create_namespace_argocd.png
    ├── 4.kubectl_get_pods_n_argocd.png
    ├── 5.kubectl_port-forward_svc_argocd.png
    ├── 6.localhost.png
    ├── 7.kubectl_apply_manifests.png
    ├── 9.kubectl_get_svc_argocd.png
    ├── 10.argocd_password.png
    ├── 11.argocd_app_nginx.png
    └── 12.nginx_localhost.png
```

### **Step 2: Prepare Sample Application Manifests**

Create a simple Nginx deployment and service. Save the following files under `manifests/app/`.

#### `manifests/app/nginx-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:stable
        ports:
        - containerPort: 80
```

#### `manifests/app/nginx-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

### **Step 3: Push Project to GitHub**

Before applying the ArgoCD Application manifest, push your project to GitHub so ArgoCD can sync the application.

#### **1. Initialize Git Repository**

```bash
git init
```

#### **2. Add Files to Staging**

```bash
git add .
```

#### **3. Commit Changes**

```bash
git commit -m "Initial commit: Add project structure and sample application manifests"
```

#### **4. Add Remote Repository**

```bash
git remote add origin https://github.com/Holuphilix/gitops-argocd-aws.git
```

#### **5. Create a file named `.gitignore` in your root and add:**


```gitignore
# OS files
*.DS_Store
desktop.ini

# IDE / editor
.vscode/
*.code-workspace
.idea/

# Node / Python / other builds
node_modules/
__pycache__/

# Local env files
.env

# Large or personal files
*.iso
*.exe
*.pdf
*.docx
```

#### **6. Push to GitHub**

```bash
git branch -M main
git push -u origin main
```

**Screenshot Example:** GitHub push
![GitHub Push](./images/12.github_push.png)

### **Step 4: Create an ArgoCD Application**

ArgoCD uses a declarative Application manifest to deploy resources from Git to the cluster.

Create `manifests/argocd/nginx-app.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/Holuphilix/gitops-argocd-aws.git'
    targetRevision: main
    path: manifests/app
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### **Step 5: Apply the Application Manifest**

Deploy the ArgoCD Application:

```powershell
kubectl apply -f manifests/argocd/nginx-app.yaml
```

### **Step 6: Verify Application Deployment**

Check the application status via CLI:

```powershell
argocd app list
argocd app get nginx-app
```

Check Kubernetes resources:

```powershell
kubectl get deployments
kubectl get svc
```

**Screenshot:** Application deployed successfully

![ArgoCD Application](./images/13.argocd_app_list_ngnix.png)

### **Step 7: Access the Sample Application**

1. Port forward the service to your local machine:

```powershell
kubectl port-forward svc/nginx-service 8080:80
```

2. Open your browser and access:
   👉 [http://localhost:8080](http://localhost:8080)

You should see the default Nginx welcome page.

**Screenshot:** Nginx Application

![Nginx Application](./images/14.nginx_localhost.png.png)

✅ **Task 3 Completed** – A sample Nginx application is now deployed and accessible using ArgoCD on EKS. The project has been pushed to GitHub, enabling full GitOps workflow.


## **Task 4: Updating and Synchronizing the Application Using ArgoCD**

This task demonstrates how to update an application in Git and let ArgoCD automatically synchronize those changes to your EKS cluster, showcasing the GitOps workflow in action.

### **Step 1: Update the Application Manifest in Git**

We will update the Nginx deployment to use **3 replicas** instead of 2.

#### Edit `manifests/app/nginx-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:stable
        ports:
        - containerPort: 80
```

### **Step 2: Push the Update to GitHub**

#### **1. Stage any Changes made**

```bash
git add .
```

#### **2. Commit the Changes**

```bash
git commit -m "Update Nginx deployment to 3 replicas"
```

#### **3. Push to GitHub**

```bash
git push origin main
```

**Screenshot:** GitHub commit & push
![GitHub commit push](./images/15.github_push_update.png.png)

### **Step 3: Synchronize Changes in ArgoCD**

ArgoCD automatically detects changes in the Git repository.

#### **Option 1: Automatic Sync (if enabled)**

If `automated` sync policy is enabled (from Task 3):

```powershell
# ArgoCD will automatically sync, check status:
argocd app get nginx-app
```

You should see `Synced` and `Healthy` status.

**Screenshot:** ArgoCD sync
![ArgoCD Sync](./images/16.argocd_sync.png.png)

### **Step 4: Verify the Deployment Update**

Check Kubernetes resources:

```powershell
kubectl get deployments
kubectl get pods
```

You should see **3 replicas of Nginx running**.

**Screenshot:** kubectl get deployments
![kubectl deployment updated](./images/17.kubectl_deployment_update.png)

### **Step 5: Demonstrate Self-Healing**

1. Delete a pod manually:

```powershell
kubectl delete pod nginx-deployment-5654587fb9-rlllz -n default
```

2. ArgoCD automatically recreates the pod to match the desired state from Git.

**Screenshot:** Pod recreated
![ArgoCD self-heal](./images/18.argocd_self_heal.png)

✅ **Task 4 Completed** – The Nginx deployment has been updated via Git, ArgoCD synchronized the changes automatically, and self-healing ensures the cluster matches the desired state.

## **Task 5: Monitor Synchronization in ArgoCD (UI & CLI)**

This task focuses on observing how ArgoCD tracks and reconciles the **desired state (Git)** with the **live state (cluster)**, using both the **ArgoCD UI** and **CLI**.

### **Step 1: Verify Application Presence & Status (CLI)**

```bash
argocd app list
argocd app get nginx-app
```

Confirm:

* **SYNC STATUS:** `Synced`
* **HEALTH:** `Healthy`
* **SOURCE:** your Git repo path `manifests/app` on branch `main`
* **DESTINATION:** `https://kubernetes.default.svc` in namespace `default`

**Screenshot:** Application Presence & Status (CLI)
![Application Presence & Status (CLI)](./images/20.argocd_app_get_cli.png.png)

### **Step 2: Inspect in the ArgoCD UI**

1. Open the ArgoCD UI (LoadBalancer URL or port-forward from Task 2).
2. Navigate to **Applications → nginx-app**.
3. Review:

   * **Overview:** Sync status (`Synced/OutOfSync`), Health (`Healthy/Degraded`).
   * **Resources Graph:** Deployment and Service with real-time health.
   * **Activity/History:** Previous syncs and Git revisions.

**Screenshot:** ArgoCD UI App Synced
![ArgoCD UI Display](./images/19.argocd_ui_app_synced.png.png)

### **Step 3: Check Sync History & Differences (CLI)**

```bash
# Show revision history for nginx-app
argocd app history nginx-app

# Show what would change (if any) without modifying the cluster
argocd app diff nginx-app
```

**Screenshot:** Check Sync History
![Check Sync History](./images/21.argocd_app_history.png.png)

Use these to understand what ArgoCD is comparing between Git and the cluster.

### **Step 4: Observe Reconciliation Logs**

This helps you **watch the controller reconciling** state (useful for learning/visibility):

```bash
# Application Controller (reconciliation loop)
kubectl logs -n argocd statefulset/argocd-application-controller -f
```

### **Step 5: Update README & Add Screenshots**

Document what you observed:

* Current **Sync** and **Health** states.
* A snippet of **`argocd app get nginx-app`** output.
* A brief note on **History** and **Diff**.
* Screenshots saved to `images/`:, 

  * `images/19.argocd_ui_app_synced.png`
  * `images/20.argocd_app_get_cli.png`
  * `images/21.argocd_app_history.png`

### **Project Directory Structure (after Task 5)**

```bash
gitops-argocd-aws/
├── README.md
├── manifests/
│   ├── app/
│   │   ├── nginx-deployment.yaml
│   │   └── nginx-service.yaml
│   └── argocd/
│       ├── nginx-app.yaml
│       └── argocd-server-lb.yaml
└── images/
    ├── 1.aws_configure.png
    ├── 2.kubectl_get_nodes.png
    ├── 3.kubectl_create_namespace_argocd.png
    ├── 4.kubectl_get_pods_n_argocd.png
    ├── 5.kubectl_port-forward_svc_argocd.png
    ├── 6.localhost.png
    ├── 7.kubectl_apply_manifests.png
    ├── 9.kubectl_get_svc_argocd.png
    ├── 10.argocd_password.png
    ├── 11.argocd_app_nginx.png
    ├── 12.nginx_localhost.png
    ├── 13.argocd_app_list_nginx.png
    ├── 14.nginx_localhost.png
    ├── 15.github_push_update.png
    ├── 16.argocd_sync.png
    ├── 17.kubectl_deployment_update.png
    ├── 18.argocd_self_heal.png
    ├── 19.argocd_ui_app_synced.png
    ├── 20.argocd_app_get_cli.png
    └── 21.argocd_app_history.png
```

✅ **Task 5 Completed** — You verified and monitored synchronization using both the ArgoCD UI and CLI.

## **Task 6: Cleanup**

This task will remove all Kubernetes resources and the EKS cluster created for this project.

### **Step 1: Delete the ArgoCD Application**

```bash
kubectl delete -f manifests/argocd/nginx-app.yaml
```

**Explanation:** This deletes the Nginx ArgoCD Application and stops ArgoCD from managing the resources.

### **Step 2: Delete Sample Application Resources**

```bash
kubectl delete -f manifests/app/nginx-deployment.yaml
kubectl delete -f manifests/app/nginx-service.yaml
```

**Explanation:** Removes the Nginx deployment and service from the cluster.

### **Step 3: Delete ArgoCD Namespace**

```bash
kubectl delete namespace argocd
```

**Explanation:** Deletes all ArgoCD components and namespace from the cluster.

### **Step 4: Delete the EKS Cluster**

```bash
eksctl delete cluster --name gitops-eks-cluster --region us-east-1
```

**Explanation:** This command deletes the entire EKS cluster along with all nodes and resources managed by it. ⚠️ Replace `--name` and `--region` with your cluster’s actual name and AWS region.

### **Step 5: Final Push to GitHub**

Even after cleanup, ensure your project repo contains the latest documentation:

```bash
git add .
git commit -m "Add Task 6 cleanup steps and finalize README"
git push origin main
```

**Explanation:** This ensures the README.md reflects the full project workflow, including cleanup.

## **Conclusion**

Congratulations! 🎉

You have successfully:

* Set up an Amazon EKS cluster.
* Installed and configured ArgoCD for GitOps workflows.
* Deployed and updated a sample application using ArgoCD.
* Observed automatic synchronization and self-healing.
* Cleaned up all resources to avoid unnecessary AWS charges.

This project demonstrates **full GitOps workflow** using ArgoCD on AWS and provides a foundation for managing cloud-native applications efficiently.

## **Author**

### **Philip Oluwaseyi Oludolamu**

**Email: [oluphilix@gmail.com](mailto:oluphilix@gmail.com)**

**GitHub: [github.com/Holuphilix](https://github.com/Holuphilix)**

**LinkedIn: [linkedin.com/in/philipoludolamu](https://linkedin.com/in/philipoludolamu)**
