# 🚀 GitHub Actions CI/CD Pipeline for Java Application (Tomcat Deployment)

## 📌 Overview

This project demonstrates how to implement a complete CI/CD pipeline using GitHub Actions for a Java application built with Maven and deployed on Apache Tomcat.

It replaces traditional CI/CD tools like Jenkins by leveraging GitHub-native automation.

---

## ⚙️ What is GitHub Actions?

GitHub Actions is a CI/CD platform that allows you to automate your build, test, and deployment pipeline directly inside GitHub.

### Key Components

| Component   | Description                               | Jenkins Equivalent |
| ----------- | ----------------------------------------- | ------------------ |
| Workflow    | Automation pipeline defined in YAML       | Pipeline           |
| Event       | Trigger for workflow (push, PR, schedule) | Webhook            |
| Job         | Group of steps running on same runner     | Stage              |
| Step        | Individual task/command                   | Step               |
| Action      | Reusable component                        | Plugin             |
| Runner      | Machine executing jobs                    | Slave/Agent        |
| Environment | Deployment target (dev, prod)             | -                  |
| Secrets     | Secure storage for sensitive data         | Credentials        |
| Cache       | Speeds up builds by reusing dependencies  | -                  |

---

## 🔄 CI/CD Pipeline Flow

```
Code → Build → Test → Artifact → Deploy
```

All stages are handled using GitHub Actions.

---

## 📁 Project Setup

1. Clone or download the project:

   ```
   https://github.com/SumanAddi/github-actions-project.git
   ```

2. Create a new repository in your GitHub account.

3. Upload project files (exclude `.github/workflows` if you want to configure from scratch).

4. Go to:

   ```
   GitHub → Actions → Search → "Java with Maven" → Configure
   ```

---

## ⚡ Workflow Example (Build + Deploy)

```yaml
name: Java CI/CD Pipeline to Tomcat

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Build with Maven
        run: mvn clean package -DskipTests

      - name: Upload WAR artifact
        uses: actions/upload-artifact@v4
        with:
          name: myapp
          path: target/*.war

  deploy:
    runs-on: ubuntu-latest
    needs: build
    environment: production

    steps:
      - name: Download WAR artifact
        uses: actions/download-artifact@v4
        with:
          name: myapp

      - name: Deploy to Tomcat
        run: |
          WAR_FILE=$(ls *.war)
          curl -v --upload-file "$WAR_FILE" \
          "http://${{ secrets.TOMCAT_USER }}:${{ secrets.TOMCAT_PASSWORD }}@${{ secrets.TOMCAT_HOST }}/manager/text/deploy?path=/myapp&update=true"
```

---

## 🔐 GitHub Secrets

Configure the following secrets:

| Secret Name     | Value               |
| --------------- | ------------------- |
| TOMCAT_USER     | tomcat              |
| TOMCAT_PASSWORD | your-password       |
| TOMCAT_HOST     | your-server-ip:8080 |

---

## 🖥️ Tomcat Setup (Amazon Linux 2023)

```bash
dnf install java-17-amazon-corretto -y
wget https://dlcdn.apache.org/tomcat/tomcat-11/v11.0.15/bin/apache-tomcat-11.0.15.tar.gz
tar -zxvf apache-tomcat-11.0.15.tar.gz
sh apache-tomcat-11.0.15/bin/startup.sh
```

Access:

```
http://<IP>:8080
```

---

## 📦 Artifacts

* WAR file is generated inside:

  ```
  target/*.war
  ```
* Stored temporarily in GitHub Actions runner
* Can be reused across jobs

---

## 🏃 Runners

### GitHub Hosted

* Ubuntu, Windows, macOS
* No setup required

### Self-Hosted

* Custom EC2/Linux machine
* Better control and performance

---

## 🔧 Self-Hosted Runner Setup

```bash
mkdir actions-runner && cd actions-runner
curl -o runner.tar.gz -L https://github.com/actions/runner/releases
tar xzf runner.tar.gz
./config.sh
./run.sh
```

Then update workflow:

```yaml
runs-on: [self-hosted, linux]
```

---

## ⚡ Caching (Improve Performance)

```yaml
- name: Cache Maven packages
  uses: actions/cache@v4
  with:
    path: ~/.m2/repository
    key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
```

---

## 🔍 SonarQube Integration

### Setup

* Install SonarQube server
* Access: `http://<IP>:9000`
* Default login: `admin/admin`

### Secrets

| Secret      | Description   |
| ----------- | ------------- |
| SONAR_HOST  | SonarQube URL |
| SONAR_TOKEN | Access token  |

### Add to Workflow

```yaml
- name: SonarQube Analysis
  run: mvn sonar:sonar \
    -Dsonar.projectKey=project \
    -Dsonar.host.url=${{ secrets.SONAR_HOST }} \
    -Dsonar.login=${{ secrets.SONAR_TOKEN }}
```

---

## ✅ Deployment Verification

```bash
curl -I http://<TOMCAT_HOST>/myapp
```

---

## 📚 Key Learnings

* End-to-end CI/CD using GitHub Actions
* Maven build automation
* Artifact management
* Secure secrets handling
* Deployment to Tomcat
* Self-hosted runners setup
* SonarQube integration

---

## 🏁 Conclusion

This project provides a complete real-world implementation of CI/CD using GitHub Actions, replacing Jenkins with a modern, GitHub-native solution.

---

## 📌 Author

**Suman Addi**

---
