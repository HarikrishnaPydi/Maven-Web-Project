// ✅ Main Scripted Pipeline
node {
    notifyBuild('STARTED')

    try {
        echo "Git branch name: ${env.BRANCH_NAME}"
        echo "Build number is: ${env.BUILD_NUMBER}"
        echo "Node name is: ${env.NODE_NAME}"

        def mavenHome = tool name: "maven-3.9.9"

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
            deploy adapters: [tomcat9(credentialsId: 'tomcat-credential', path: '', url: 'http://http://54.160.187.51:8080/')], contextPath: null, war: '**/maven-web-application.war'
        }

        currentBuild.result = 'SUCCESS'

    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        notifyBuild(currentBuild.result)
        sendEmailNotification(currentBuild.result)
    }
}

// ✅ Slack Notification Function
def notifyBuild(String buildStatus = 'STARTED') {
    def colorCode = '#FF0000' // Red by default
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [#${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    if (buildStatus == 'STARTED') {
        colorCode = '#FFFF00' // Yellow
    } else if (buildStatus == 'SUCCESS') {
        colorCode = '#00FF00' // Green
    }

    slackSend(channel: '#airtel-devteam', color: colorCode, message: summary)
}

// ✅ Email Notification Function
def sendEmailNotification(String buildStatus = 'STARTED') {
    emailext (
        subject: "Jenkins Build - ${buildStatus}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
        body: """
            <p><b>Status:</b> ${buildStatus}</p>
            <p><b>Job:</b> ${env.JOB_NAME}</p>
            <p><b>Build #:</b> ${env.BUILD_NUMBER}</p>
            <p><b>URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
        """,
        mimeType: 'text/html',
        to: 'haripydi777@gmail.com'
    )
}
