Here's a **detailed, step-by-step README guide** covering your three key topics for Jenkins + Docker integration:

---

# 📘 Jenkins + Docker + Webhook: Complete Setup Guide

This guide covers:

1. ✅ Fixing Docker permission issue (`/var/run/docker.sock`)
2. 🔐 Doing Docker login using **Jenkins credentials**
3. 🔄 Setting up **GitHub Webhook** with **Declarative Pipeline**

---

## 🐳 1. Fix: `docker.sock` Permission Issue

### ❌ Problem:

You get this error in Jenkins:

```
Got permission denied while trying to connect to the Docker daemon socket at /var/run/docker.sock
```

### ✅ Solution:

### **Step-by-step**:

1. **Check Docker group ownership**:

   ```bash
   ls -l /var/run/docker.sock
   ```

   You should see something like:

   ```
   srw-rw---- 1 root docker 0 Jul 17 12:00 /var/run/docker.sock
   ```

2. **Add `jenkins` user to `docker` group**:

   ```bash
   sudo usermod -aG docker jenkins
   ```

3. **Restart Jenkins & Docker**:

   ```bash
   sudo systemctl restart jenkins
   sudo systemctl restart docker
   ```

4. **Reboot the VM** (to apply group changes):

   ```bash
   sudo reboot
   ```

5. **Verify group for Jenkins**:
   After reboot:

   ```bash
   id jenkins
   ```

   Output should include `docker` group.

> ⚠️ **Note**: If Jenkins is running inside a container, you need to mount the Docker socket correctly:

```yaml
-v /var/run/docker.sock:/var/run/docker.sock
```

---

## 🔐 2. Docker Login in Jenkins Pipeline (Securely)

### 💡 Goal:

Log in to Docker Hub in your pipeline without exposing your password.

### 🔐 Step-by-step:

#### **Step 1: Create Docker Hub Token**

* Go to [Docker Hub → Account Settings → Security](https://hub.docker.com/settings/security)
* Click **New Access Token**
* Name it `jenkins-token`
* Copy the token (you won’t see it again)

---

#### **Step 2: Add Jenkins Credentials**

Go to:
**Jenkins → Manage Jenkins → Credentials → (Global) → Add Credentials**

* **Kind**: Username and Password
* **Username**: your Docker Hub username
* **Password**: paste the Docker Hub token
* **ID**: `dockerhub-creds` (you’ll use this in the pipeline)

---

#### **Step 3: Use in Declarative Pipeline**

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'yourusername/yourimage:latest'
    }

    stages {
        stage('Docker Login & Build') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                     usernameVariable: 'DOCKER_USER',
                                                     passwordVariable: 'DOCKER_PASS')]) {
                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker build -t $IMAGE_NAME .
                            docker push $IMAGE_NAME
                        '''
                    }
                }
            }
        }
    }
}
```

> ✅ Docker login is now done securely using credentials.

---

## 🔄 3. GitHub Webhook with Jenkins (Declarative Pipeline)

### 🧩 Goal:

Trigger Jenkins pipeline automatically on GitHub push.

---

### 🔧 Step-by-step:

#### **Step 1: Enable GitHub Webhook in Jenkins Job**

* Open your pipeline job in Jenkins
* Click **Configure**
* Under **Build Triggers**, check ✅:

  ```
  GitHub hook trigger for GITScm polling
  ```

---

#### **Step 2: GitHub Webhook Setup**

Go to your GitHub repo:

* Settings → Webhooks → Add Webhook

Enter:

* **Payload URL**:
  `http://<your-jenkins-url>/github-webhook/`
  (e.g., `http://your-public-ip:8080/github-webhook/`)
* **Content Type**: `application/json`
* ✅ Just push event is enough
* Click **Add Webhook**

> ⚠️ Jenkins must be accessible from GitHub (use **ngrok** or **public IP** if running locally)

---

#### **Step 3: Use Git in Declarative Pipeline**

```groovy
pipeline {
    agent any

    triggers {
        githubPush() // Triggers on GitHub push
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-creds-id',
                    url: 'https://github.com/your-username/your-repo.git',
                    branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                // same as docker build/login steps above
            }
        }
    }
}
```

---

## ✅ Final Notes

| Step                   | Key Reminder                               |
| ---------------------- | ------------------------------------------ |
| Docker.sock permission | Add `jenkins` to `docker` group & reboot   |
| Docker login           | Use Docker token via `withCredentials`     |
| GitHub webhook         | Use `githubPush()` trigger + reachable URL |

---

Would you like this as a downloadable `README.md` file too?
