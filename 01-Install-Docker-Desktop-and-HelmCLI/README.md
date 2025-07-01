# 安装 Docker Desktop 和 HELM CLI

## 步骤-01：介绍

1. 安装 Docker Desktop
2. 在本地桌面安装 Helm CLI

## 步骤-02：Docker Desktop - 定价、注册、下载

- [Docker Desktop 定价](https://www.docker.com/pricing/)
- [注册 Docker Hub](https://hub.docker.com/)
- [下载 Docker Desktop](https://www.docker.com/products/docker-desktop/)

## 步骤-03：安装 Docker Desktop

### 步骤-03-01：MACOS：安装 Docker Desktop

```bash
# Install Docker Desktop
Copy Docker dmg to Applications folder

# Create Docker Hub Account
https://hub.docker.com

# Signin Docker Desktop 
Open Docker Desktop and SignIn to Docker Hub
```

### 步骤-03-02：WINDOWS：安装 Docker Desktop

```bash
# Download Docker Desktop
https://www.docker.com/products/docker-desktop/

# Install Docker Desktop on Windows
Run the "Docker Desktop Installer.exe"

# Create Docker Hub Account
https://hub.docker.com

# Signin Docker Desktop 
Open Docker Desktop and SignIn to Docker Hub

# Configure kubectl cli on Windows PATH
C:\Program Files\Docker\Docker\Resources\bin
```

## 步骤-04：启用 Kubernetes 集群

- **附加参考：** [Docker Desktop - k8s 集群](https://docs.docker.com/desktop/kubernetes/)

```bash
# Enable Kubernetes Cluster
- Go to Settings -> Enable Kubernetes
- Apply and Restart
- Kubernetes Cluster Installation: Install
- Wait for 5 to 10 minutes for Kubernetes Cluster to come up
```

## 步骤-05：为 Docker Desktop k8s 集群配置 kubectl 的 kubeconfig

```bash
# Verify if kubectl installed (Docker desktop should install kubectl automatically)
which kubectl

# Verify kubectl version
kubectl version 
kubectl version --short
kubectl version --client --output=yaml

# List Config Contexts
kubectl config get-contexts

# Config Current Context
kubectl config current-context

# Config Use Context (Only if someother context is present in current-context output)
kubectl config use-context docker-desktop

# List Kubernetes Nodes
kubectl get nodes
```

## 步骤-06：使用示例应用程序验证我们的 k8s 集群是否正常运行

- [StackSimplify Docker 镜像](https://github.com/stacksimplify?tab=packages)
- [本演示中使用的 Docker 镜像](https://github.com/users/stacksimplify/packages/container/package/kubenginxhelm)

```bash
# Review Kubernetes Manifests
Folder: kube-manifests
deployment.yaml
service.yaml

# Deploy k8s Resources to Docker Desktop k8s Cluster
kubectl apply -f kube-manifests/

# List k8s Deployments
kubectl get deploy

# List k8s pods
kubectl get pods

# List k8s Services
kubectl get svc

# Access Application
http://localhost:31300
or
http://127.0.0.1:31300

# Uninstall k8s Resources from Docker Desktop k8s cluster
kubectl delete -f kube-manifests/

# List pods, svc, deploy
kubectl get pods
kubectl get svc
kubectl get deploy
```

## 步骤-07：使用包管理器安装 Helm

- [安装 Helm](https://helm.sh/docs/intro/install/)

```bash
# MacOS
brew install helm

# From Chocolatey (Windows)
choco install kubernetes-helm

# From Scoop (Windows)
scoop install helm

# Verify Helm version
helm version

# Helm Environment variables
helm env
```

## 步骤-08：Windows 使用包安装 Helm CLI

```bash
# Helm Releases - Download Windows amd64
https://github.com/helm/helm/releases

# Uzip the file
helm-v3.12.3-windows-amd64.zip

# Copy to C:\helm Drive
C:\ Drive

# Set Path
C:\helm\windows-amd64
```

## 附加可选步骤：安装 kubectl（如果默认未安装）

```bash
# Download & Install kubectl
https://kubernetes.io/docs/tasks/tools/
MacOS kubectl Install: https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/
# Downlaod MacOS Intel (Update kubectl version)
curl -LO "https://dl.k8s.io/release/v1.27.2/bin/darwin/amd64/kubectl"
# Download MacOS Apple Silicon (Update kubectl version)
curl -LO "https://dl.k8s.io/release/v1.27.2/bin/darwin/arm64/kubectl"

# Make Binary executable
chmod +x ./kubectl

# Move the kubectl binary to a file location on your system PATH.
sudo mv ./kubectl /usr/local/bin/kubectl
ls -lrta /usr/local/bin/kubectl

# Verify kubectl version
kubectl version 
kubectl version --short
kubectl version --client --output=yaml
```
