# 📂 Final Project Structure
```
devops-cicd/
├── app/
│   └── app.py
├── docker/
│   └── Dockerfile
├── jenkins/
│   └── Jenkinsfile
└── README.md
```

---

1. app/app.py
   ```
   print("Hello, World from Dockerized App!")
   ```
3. docker/Dockerfile
   ```
   FROM python:3.12-slim
   WORKDIR /app
   COPY app/app.py .
   CMD ["python", "app.py"]
   ```

4. jenkins/Jenkinsfile

```
pipeline {
    agent any
    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/sanketrandhe/devops-cicd'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t pythonapp -f docker/Dockerfile .'
            }
        }
        stage('Deploy Container') {
            steps {
                sh '''
                  docker stop pythonapp || true
                  docker rm pythonapp || true
                  docker run -d --restart always --name pythonapp pythonapp
                '''
            }
        }
        stage('Check Logs') {
            steps {
                sh 'docker logs pythonapp || echo "Container not found!"'
            }
        }
    }
}
```
6. README.md
# DevOps CI/CD Project: Jenkins + Docker + GitHub + AWS EC2

This project demonstrates a **CI/CD pipeline** where:
- Jenkins pulls code from GitHub
- Builds a Docker image
- Deploys the container on an AWS EC2 instance
- Prints "Hello, World from Dockerized App!"

---

## Tech Stack
- AWS EC2 (Amazon Linux 2023)
- Docker
- Jenkins
- Git & GitHub

---

## Setup Instructions

### 1. Launch EC2
- Amazon Linux 2023
- Open ports: 22 (SSH) and 8080 (Jenkins)

SSH:
```bash
ssh -i your-key.pem ec2-user@<EC2-PUBLIC-IP>
```

---

### 2. Install Packages

```bash
sudo yum update -y
sudo yum install git docker java-17-amazon-corretto -y
sudo systemctl enable --now docker
sudo usermod -aG docker ec2-user
```

*Log out & log in again.*

---

### 3. Install Jenkins

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install jenkins -y
sudo systemctl enable --now jenkins
```

Get initial admin password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Access Jenkins:

```
http://<EC2-PUBLIC-IP>:8080
```

---

### 4. GitHub Token

1. GitHub → Settings → Developer Settings → Personal Access Tokens → Generate new token
2. Select `repo` scope
3. Copy token → use instead of password when pushing code from EC2

---

### 5. Push Code to GitHub

```bash
git init
git remote add origin https://github.com/sanketrandhe/devops-cicd.git
git add .
git commit -m "Initial commit with fixed Dockerfile and Jenkinsfile"
git branch -M main
git push -u origin main
```

---

### 6. Jenkins Pipeline

1. Create new Pipeline in Jenkins → paste `Jenkinsfile`
2. Run build → Jenkins will:

   * Clone repo
   * Build Docker image
   * Stop/remove old container
   * Run new container
   * Show logs

---

### 7. Verify

On EC2:

```bash
docker ps
docker logs pythonapp
```

**Expected output:**

```
Hello, World from Dockerized App!
```
### Image of output:
<img width="1920" height="1080" alt="Screenshot (260)" src="https://github.com/user-attachments/assets/e3d219e3-c9ea-4157-9717-dd6cd2ac0adb" />


---


### Badges

![Docker](https://img.shields.io/badge/Docker-Enabled-blue)
![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-green)
![AWS](https://img.shields.io/badge/AWS-EC2-orange)

