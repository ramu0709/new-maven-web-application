def branchName = env.BRANCH_NAME ?: sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()

echo "The Jenkins Job Name is: ${env.JOB_NAME}"         // ✅ Always available
echo "The Jenkins Node Name is: ${env.NODE_NAME}"       // ✅ Always available
echo "Git Branch: ${branchName}"                        // ✅ Fallback if env.BRANCH_NAME not set

node {
    def mavenHome = tool name: "Maven3.9.8"

    buildName "pipe - #${BUILD_NUMBER}"

    echo "The Job name is: ${env.JOB_NAME}"
    echo "The Node name is: ${env.NODE_NAME}"
    echo "The Build Number is: ${env.BUILD_NUMBER}"
    echo "The Jenkins Home directory is: ${JENKINS_HOME}"

    properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '2')),
        pipelineTriggers([githubPush()])
    ])

    stage('Checkout Code') {
        git branch: "main", credentialsId: "9c54f3a6-d28e-4f8f-97a3-c8e939dcc8ff", url: "https://github.com/ramu0709/new-maven-web-application.git"
    }

    stage('Build') {
        sh "${mavenHome}/bin/mvn clean package"
    }

    stage('Execute SonarQube Report') {
        withSonarQubeEnv('sonarqube') {
            sh "${mavenHome}/bin/mvn sonar:sonar"
        }
    }

    stage('Upload Artifact into Nexus') {
        sh "${mavenHome}/bin/mvn deploy"
    }

    stage('Deploy App Into Tomcat Server') {
        sh 'cp target/maven-web-application.war /opt/tomcat/webapps/'
    }

    /*
    stage('Send Email Notification') {
        emailext body: '''Build Over - Scripted way

Regards,
Batman''', subject: 'Build Over - Scripted way', to: '*****@gmail.com'
    }
    */
}
