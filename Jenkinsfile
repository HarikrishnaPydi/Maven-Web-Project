// ✅ Main Pipeline
node {
    notifyBuild('STARTED')

    try {
        echo "Git branch name: ${env.BRANCH_NAME}"
        echo "Build number is: ${env.BUILD_NUMBER}"
        echo "Node name is: ${env.NODE_NAME}"

        def mavenHome = tool name: "maven-3.9.9"

        stage('Git Checkout') {
            git branch: 'hotfix', credentialsId: 'gtihub-credentials', url: 'https://github.com/HarikrishnaPydi/Maven-Web-Project.git'
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
            deploy adapters: [tomcat9(credentialsId: 'tomcat-credential', path: '', url: 'http://54.172.174.168:8080/')], contextPath: null, war: '**/maven-web-application.war'
        }

        // ✅ Mark success
        currentBuild.result = 'SUCCESS'

    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        notifyBuild(currentBuild.result)
    }
}
// ✅ Define the Slack Notification Function
def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus = buildStatus ?: 'SUCCESS'

    // Default values
    def colorCode = '#FF0000' // red
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [#${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    // Set color based on build status
    if (buildStatus == 'STARTED') {
        colorCode = '#FFFF00' // yellow
    } else if (buildStatus == 'SUCCESS') {
        colorCode = '#00FF00' // green
    } else {
        colorCode = '#FF0000' // red
    }

    // Send Slack notification
    slackSend(channel: '#airtel-devteam', color: colorCode, message: summary)
}
