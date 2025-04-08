node {
    def mavenHome = tool name: "Maven3.9.8"

    // Dynamically resolve Git branch name
    def branchName = env.BRANCH_NAME ?: sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()

    // Set build name for display in Jenkins
    buildName "pipe - #${BUILD_NUMBER}"

    // Print basic job environment info
    echo "The Jenkins Job Name is: ${env.JOB_NAME}"         // ✅ Always available
    echo "The Jenkins Node Name is: ${env.NODE_NAME}"       // ✅ Always available
    echo "Git Branch: ${branchName}"                        // ✅ Fallback if env.BRANCH_NAME not set

    // Set retention and trigger policies
    properties([
        buildDiscarder(logRotator(numToKeepStr: '2')),
        pipelineTriggers([githubPush()])
    ])

    stage('Checkout Code') {
        git branch: 'main',
            credentialsId: '9c54f3a6-d28e-4f8f-97a3-c8e939dcc8ff',
            url: 'https://github.com/ramu0709/new-maven-web-application.git'
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
        // Determine Nexus repository and version based on branch
        def repository = branchName.contains("main") || branchName.contains("master") ? "sample-release" : "sample-snapshot"
        def version = branchName.contains("main") || branchName.contains("master") ? "0.0.1" : "0.0.1-SNAPSHOT"

        nexusArtifactUploader artifacts: [[
            artifactId: 'maven-web-application',
            classifier: '',
            file: 'target/maven-web-application.war',
            type: 'war'
        ]],
        credentialsId: 'nexus',
        groupId: 'Batman',
        version: version,
        repository: repository,
        nexusUrl: '172.21.40.70:8081/',
        nexusVersion: 'nexus3',
        protocol: 'http'
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
