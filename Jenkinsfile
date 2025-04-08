node {
    def mavenHome = tool name: "Maven3.9.8"

    // ✅ Set build name for display in Jenkins
    buildName "pipe - #${BUILD_NUMBER}"

    // ✅ Print basic job environment info
    echo "✅ The Jenkins Job Name is: ${env.JOB_NAME}"         // ℹ️ Always available
    echo "✅ The Jenkins Node Name is: ${env.NODE_NAME}"       // ℹ️ Always available

    // ✅ Set retention and trigger policies
    properties([
        buildDiscarder(logRotator(numToKeepStr: '2')),
        pipelineTriggers([githubPush()])
    ])

    stage('✅ Checkout Code') {
        git branch: 'main',
            credentialsId: '9c54f3a6-d28e-4f8f-97a3-c8e939dcc8ff',
            url: 'https://github.com/ramu0709/new-maven-web-application.git'
    }

    // ✅ Dynamically resolve Git branch name (after checkout)
    def branchName = env.BRANCH_NAME ?: sh(
        script: "git rev-parse --abbrev-ref HEAD",
        returnStdout: true
    ).trim()

    echo "✅ Git Branch: ${branchName}"                        // ℹ️ Fallback if env.BRANCH_NAME not set

    stage('✅ Build') {
        sh "${mavenHome}/bin/mvn clean package"
    }

    stage('✅ Execute SonarQube Report') {
        withSonarQubeEnv('sonarqube') {
            withEnv(["SONAR_TOKEN=sonar-token"]) { // 🔐 Token must match Jenkins Global Configuration
                sh "${mavenHome}/bin/mvn sonar:sonar -Dsonar.login=${env.SONAR_TOKEN}"
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
        // ℹ️ Determine Nexus repository and version based on branch
        def repository = (branchName == "main" || branchName == "master") ? "sample-release" : "sample-snapshot"
        def version = (branchName == "main" || branchName == "master") ? "0.0.1" : "0.0.1-SNAPSHOT"

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

    stage('✅ Deploy App Into Tomcat Server') {
        sh 'cp target/maven-web-application.war /opt/tomcat/webapps/'
    }

    /*
    stage('✅ Send Email Notification') {
        emailext body: '''Build Over - Scripted way

Regards,
Batman''', subject: 'Build Over - Scripted way', to: '*****@gmail.com'
    }
    */
}
