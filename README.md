# Jenkins Scripted Pipeline: Real-Time CI/CD Project 🚀

This project demonstrates a real-time **Jenkins Scripted Pipeline** that automates the entire CI/CD workflow using:

- GitHub
- Maven
- SonarQube
- Nexus Repository Manager
- Tomcat Server
- Slack Notifications
- Email Alerts

---

## 🧭 CI/CD Pipeline Flow

![CI/CD Flow](https://files.oaiusercontent.com/file-000000004c7461f8a982cf2d3aa8eb91)

---

## 🧱 Technologies Used

| Tool         | Purpose                            |
|--------------|-------------------------------------|
| GitHub       | Source Code Repository              |
| Maven        | Build Tool                          |
| SonarQube    | Code Quality and Static Analysis    |
| Nexus        | Artifact Repository (WAR uploads)   |
| Tomcat       | Application Deployment Server       |
| Slack        | Real-time Build Notifications       |
| Email        | Build Status via Email              |

---

## 🛠️ Step-by-Step Guide

### ✅ 1. Git Checkout

Pulls code from the `development` branch of your GitHub repository.

```groovy
stage('Git Checkout') {
    git branch: 'development', credentialsId: 'gtihub-credentials', url: 'https://github.com/HarikrishnaPydi/Maven-Web-Project.git'
}
stage('Build') {
    sh "${mavenHome}/bin/mvn clean package"
}
stage('SonarQube Report') {
    sh "${mavenHome}/bin/mvn clean sonar:sonar package"
}
stage('Deploy to Nexus') {
    sh "${mavenHome}/bin/mvn sonar:sonar deploy"
}
stage('Deploy to Tomcat') {
    deploy adapters: [tomcat9(credentialsId: 'tomcat-credential', path: '', url: 'http://54.160.187.51:8080/')], contextPath: null, war: '**/maven-web-application.war'
}
slackSend(channel: '#airtel-devteam', color: colorCode, message: summary)
emailext (
    subject: "Jenkins Build - ${buildStatus}",
    ...
    to: 'haripydi777@gmail.com'
)
✅ Error Handling
The pipeline uses try-catch-finally to catch exceptions and ensure notifications are always sent.

