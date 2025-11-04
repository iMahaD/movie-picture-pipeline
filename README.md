# 🎬 Movie Picture Pipeline

## 📖 Overview
This project demonstrates a **CI/CD pipeline** that automates the build, test, and deployment of a full-stack web application using **AWS EKS, Terraform, Docker, and GitHub Actions**.  
The application includes:
- A **Flask (Python)** backend API that serves movie data.
- A **React** frontend that displays the movie list and details.

Both components are containerized, stored in **Amazon ECR**, and deployed on a **Kubernetes cluster (EKS)** provisioned via Terraform.

---

## 🏗️ Architecture

### Components
| Layer | Technology | Purpose |
|-------|-------------|----------|
| **Infrastructure** | Terraform | Provisions AWS EKS, ECR, IAM roles |
| **CI/CD** | GitHub Actions | Automates build, test, and deploy for backend & frontend |
| **Containers** | Docker | Packages applications for deployment |
| **Cluster Management** | Amazon EKS + kubectl + Kustomize | Deploys and manages workloads |
| **Storage** | Amazon ECR | Stores container images |

---

## ⚙️ Pipeline Stages

### 1. **Infrastructure Provisioning (Terraform)**
Terraform provisions the foundational cloud resources:
- **EKS Cluster**
- **ECR Repositories (backend & frontend)**
- **IAM Roles for GitHub Actions and Nodes**

Run locally:
```bash
cd setup
terraform init
terraform apply
```

Validate:
```bash
aws eks describe-cluster --name cluster --region us-east-1
```
✅ Expected output includes `"status": "ACTIVE"`

---

### 2. **Backend Continuous Integration**
**File:** `.github/workflows/backend-ci.yml`

- **Lints Python code using flake8**
- **Runs unit tests with pytest**
- **Builds the backend Docker image**

---

### 3. **Backend Continuous Deployment**
**File:** `.github/workflows/backend-cd.yml`

- Builds and pushes backend image to Amazon ECR
- Deploys backend to EKS using manifests in `starter/backend/k8s`
- Verifies rollout and displays the service

**Backend ECR:**
```
433597223496.dkr.ecr.us-east-1.amazonaws.com/backend
```

**Backend LoadBalancer:**
```
http://ad157b3670bd84d27865f41c283aebcd-2112857283.us-east-1.elb.amazonaws.com
```

Example test:
```bash
curl http://ad157b3670bd84d27865f41c283aebcd-2112857283.us-east-1.elb.amazonaws.com/movies
```

✅ Expected output:
```json
{"movies":[
  {"id":"123","title":"Top Gun: Maverick"},
  {"id":"456","title":"Sonic the Hedgehog"},
  {"id":"789","title":"A Quiet Place"}
]}
```

---

### 4. **Frontend Continuous Integration**
**File:** `.github/workflows/frontend-ci.yml`

- Installs and tests React app build
- Runs lint and unit tests
- Verifies Docker build

---

### 5. **Frontend Continuous Deployment**
**File:** `.github/workflows/frontend-cd.yml`

- Builds and pushes frontend Docker image to ECR
- Deploys frontend to EKS using manifests in `starter/frontend/k8s`
- Injects backend API URL as environment variable:
```yaml
REACT_APP_MOVIE_API_URL: "http://ad157b3670bd84d27865f41c283aebcd-2112857283.us-east-1.elb.amazonaws.com"
```

**Frontend ECR:**
```
433597223496.dkr.ecr.us-east-1.amazonaws.com/frontend
```

**Frontend LoadBalancer:**
```
http://aab5bb0f4d4dc41eaa0b16f854d45b1a-592954737.us-east-1.elb.amazonaws.com
```

✅ When opened in a browser, you should see:
- Movie List: Top Gun, Sonic, A Quiet Place
- Movie Details displayed dynamically

---

## 🔑 GitHub Secrets Configuration

| Secret Name | Description |
|-------------|-------------|
| `AWS_ACCESS_KEY_ID` | AWS access key |
| `AWS_SECRET_ACCESS_KEY` | AWS secret key |
| `AWS_REGION` | AWS region (e.g., us-east-1) |
| `AWS_ACCOUNT_ID` | AWS account number (433597223496) |
| `AWS_ROLE_TO_ASSUME` | IAM role for GitHub Actions |
| `ECR_REGISTRY` | AWS ECR registry URI |
| `ECR_REPOSITORY_BACKEND` | Backend ECR repository URL |
| `ECR_REPOSITORY_FRONTEND` | Frontend ECR repository URL |
| `EKS_CLUSTER_NAME` | Name of the EKS cluster |
| `REACT_APP_MOVIE_API_URL` | Backend LoadBalancer URL |

---

## 🧩 Kubernetes Manifests

Both backend and frontend use Kustomize for Kubernetes configuration.
```
starter/
├── backend/
│   └── k8s/
│       ├── deployment.yaml
│       ├── service.yaml
│       └── kustomization.yaml
└── frontend/
    └── k8s/
        ├── deployment.yaml
        ├── service.yaml
        └── kustomization.yaml
```

---

## 🌐 Accessing the Application

### Backend API
```
http://ad157b3670bd84d27865f41c283aebcd-2112857283.us-east-1.elb.amazonaws.com/movies
```

### Frontend App
```
http://aab5bb0f4d4dc41eaa0b16f854d45b1a-592954737.us-east-1.elb.amazonaws.com
```

---

## 🧠 Key Learnings

- Built a full CI/CD pipeline with GitHub Actions
- Automated ECR build and EKS deployment
- Integrated React frontend with Flask backend via LoadBalancer
- Applied Infrastructure as Code with Terraform
- Gained hands-on experience with Kubernetes manifests and service management

---

## 👩‍💻 Author

**Maha Aldosary**
