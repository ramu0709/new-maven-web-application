node {

    def mavenHome = tool name: "Maven3.9.8"
    def version = "0.0.1-SNAPSHOT"
    def repository = version.endsWith("SNAPSHOT") ? "sample-snapshot" : "sample-release"

    echo "The Job name is: ${env.JOB_NAME}"
    echo "The Node name is: ${env.NODE_NAME}"
    echo "The Build Number is: ${env.BUILD_NUMBER}"
    echo "The Jenkins Home directory is: ${JENKINS_HOME}"

    properties([
        buildDiscarder(logRotator(numToKeepStr: '2')),
        pipelineTriggers([githubPush()])
    ])

    stage('CheckoutCode') {
        git branch: 'main', credentialsId: '9c54f3a6-d28e-4f8f-97a3-c8e939dcc8ff', url: 'https://github.com/ramu0709/new-maven-web-application.git'
    }

    stage('Build') {
        sh "${mavenHome}/bin/mvn clean package"
    }

    stage('ExecuteSonarQubeReport') {
        sh "${mavenHome}/bin/mvn sonar:sonar"
    }

    stage('UploadArtifactintoNexus') {
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

    stage('DeployAppIntoTomcatServer') {
        sh 'cp target/maven-web-application.war /opt/tomcat/webapps/'
    }

    /*
    stage('SendEmailNotification') {
        emailext body: '''Build Over - Scripted way

        Regards 
        Batman''', subject: 'Build Over - Scripted way', to: '*****@gmail.com'
    }
    */
}
