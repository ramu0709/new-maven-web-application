node {
    def mavenHome = tool name: "Maven3.9.9"

    buildName "pipe - #${BUILD_NUMBER}"
    echo "✅ The Jenkins Job Name is: ${env.JOB_NAME}"
    echo "✅ The Jenkins Node Name is: ${env.NODE_NAME}"

    properties([
        buildDiscarder(logRotator(numToKeepStr: '2')),
        pipelineTriggers([githubPush()])
    ])

    stage('✅ Checkout Code') {
        git branch: 'main',
            credentialsId: '9c54f3a6-d28e-4f8f-97a3-c8e939dcc8ff',
            url: 'https://github.com/ramu0709/new-maven-web-application.git'
    }

    def branchName = env.BRANCH_NAME ?: sh(
        script: "git rev-parse --abbrev-ref HEAD",
        returnStdout: true
    ).trim()

    echo "✅ Git Branch: ${branchName}"

    stage('✅ Build') {
        sh "${mavenHome}/bin/mvn clean package" // no deploy here
    }

    stage('✅ Execute SonarQube Report') {
        withSonarQubeEnv('sonarqube') {
            withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                sh "${mavenHome}/bin/mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN"
            }
        }
    }

    stage('✅ Enforce Code Coverage with JaCoCo') {
        jacoco buildOverBuild: true,
            changeBuildStatus: true,
            deltaBranchCoverage: '80',
            deltaClassCoverage: '80',
            deltaComplexityCoverage: '80',
            deltaInstructionCoverage: '80',
            deltaLineCoverage: '80',
            deltaMethodCoverage: '80',
            maximumBranchCoverage: '80',
            maximumClassCoverage: '80',
            maximumComplexityCoverage: '80',
            maximumInstructionCoverage: '80',
            maximumLineCoverage: '80',
            maximumMethodCoverage: '80',
            minimumBranchCoverage: '80',
            minimumClassCoverage: '80',
            minimumComplexityCoverage: '80',
            minimumInstructionCoverage: '80',
            minimumLineCoverage: '80',
            minimumMethodCoverage: '80'
    }

    stage('✅ Upload Artifact into Nexus') {
        def repository = (branchName == "main" || branchName == "master") ? "sample-release" : "sample-snapshot"
        def version = (branchName == "main" || branchName == "master") ? "0.0.1" : "0.0.1-SNAPSHOT"

        withCredentials([usernamePassword(credentialsId: 'nexus-credentials', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
            echo "🔐 Uploading version: ${version} to ${repository}"

            nexusArtifactUploader artifacts: [[
                artifactId: 'maven-web-application',
                classifier: '',
                file: 'target/maven-web-application.war',
                type: 'war'
            ]],
            credentialsId: 'nexus-credentials',
            groupId: 'Batman',
            version: version,
            repository: repository,
            nexusUrl: '172.21.40.70:8081/',
            nexusVersion: 'nexus3',
            protocol: 'http'
        }
    }

    stage('✅ Deploy App Into Tomcat Server') {
        sh 'sudo cp target/maven-web-application.war /opt/tomcat/webapps/'
    }

    /*
    stage('✅ Send Email Notification') {
        emailext body: '''Build Over - Scripted
