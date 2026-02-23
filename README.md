# 🚀 Containerized Java CI/CD Pipeline with Jenkins & AWS

This project demonstrates an **end-to-end DevOps CI/CD pipeline** that automatically builds, containerizes, and deploys a Java web application using:

* ✅ **GitHub** – Source code management & webhook trigger
* ✅ **Jenkins** – CI/CD automation server
* ✅ **Maven** – Java build & packaging (WAR)
* ✅ **Docker** – Containerization
* ✅ **Docker Hub** – Container registry
* ✅ **AWS EC2** – Jenkins server + target deployment host
* ✅ **Tomcat** – Application runtime

---

## 📌 Architecture Overview

```

                👨‍💻 Developer
                     │
                     │ git push
                     ▼
                 🌐 GitHub Repo
                     │
                     │ Webhook trigger
                     ▼
            ⚙️ Jenkins Server (AWS EC2)
            --------------------------------
            • Checkout source code
            • Build WAR using Maven
            • Build Docker image (Tomcat + WAR)
            • Push image to Docker Hub
            • SSH into target EC2
            --------------------------------
                     │
                     ▼
              📦 Docker Hub Registry
                     │
                     │ docker pull
                     ▼
           🖥 Target EC2 (Docker Host)
            --------------------------------
            • Pull latest image
            • Stop old container
            • Run new container
            --------------------------------
                     │
                     ▼
               🌍 User Browser
                     │
                     ▼
        http://TARGET_EC2_IP:8080
               Java App on Tomcat

```

```
Developer → GitHub → Webhook → Jenkins (EC2)

Jenkins Pipeline:
1. Checkout source code
2. Build WAR using Maven
3. Build Docker image (Tomcat + WAR)
4. Push image to Docker Hub
5. SSH to Target EC2
6. Pull image & run container

User → Target EC2 → Docker → Tomcat → Java App
```

---

## 📂 Project Structure

```
containerized-java-pipeline/
│
├── Jenkinsfile
├── README.md
└── hello-world/
    ├── pom.xml
    ├── Dockerfile
    ├── server/
    └── webapp/
```

---

## ⚙️ Jenkins Pipeline Stages

### 1️⃣ Checkout

Clones repository from GitHub.

### 2️⃣ Build WAR

```
mvn clean package
```

Generates:

```
webapp/target/webapp.war
```

### 3️⃣ Build Docker Image

Dockerfile uses **Tomcat base image** and copies WAR:

```
FROM tomcat:9-jdk17
COPY webapp/target/webapp.war /usr/local/tomcat/webapps/ROOT.war
```

### 4️⃣ Push to Docker Hub

Image tagged using Jenkins build number:

```
pranitpotsure/hello-java:<build_number>
```

### 5️⃣ Deploy to AWS EC2

Jenkins connects via SSH and runs:

```
docker pull IMAGE
docker stop hello-java || true
docker rm hello-java || true
docker run -d -p 8080:8080 --name hello-java IMAGE
```

---

## 🔑 Jenkins Credentials Used

### Docker Hub

```
ID: dockerhub-pass
Type: Username/Password
```

### Target EC2 SSH

```
ID: target-ec2-key
Type: SSH Username with Private Key
User: ec2-user
```

---

## 🌐 Accessing the Application

After successful deployment:

```
http://<TARGET_EC2_PUBLIC_IP>:8080
```

---

## 🔁 Automatic Build Trigger

GitHub webhook is configured:

```
GitHub push → Jenkins pipeline auto-starts
```

No manual build required.

---

## 🛠 Requirements

### Jenkins EC2

* Java 17
* Maven
* Docker
* Git

### Target EC2

* Docker installed
* Port **8080** open in security group

---

## 🎯 Key DevOps Concepts Demonstrated

* CI/CD automation
* Java multi-module Maven project
* Docker containerization
* Remote deployment via SSH
* GitHub webhook integration
* AWS EC2 infrastructure usage
* Secure credential management in Jenkins

---

## ⭐ Future Improvements

* Use **AWS ECR** instead of Docker Hub
* Add **SonarQube code quality scan**
* Implement **Blue/Green deployment**
* Deploy on **Kubernetes** using provided YAML files
* Add **Slack/email notifications**

---

## 👨‍💻 Author

**Pranit Potsure**
DevOps / Cloud Enthusiast

---

If you found this project helpful, ⭐ star the repo!
