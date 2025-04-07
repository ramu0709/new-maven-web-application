node
{

 def mavenHome = tool name: "Maven3.9.8"

echo "The Job name is: ${env.JOB_NAME}"
echo "The Nod ename is: ${env.NODE_NAME}"
echo "The Build Number is: ${env.BUILD_NUMBER}"
echo "The Jenkins Home directory is: ${JENKINS_HOME}"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '2')), pipelineTriggers([githubPush()])])
 
 stage('CheckoutCode')
 {
  git branch: 'main', credentialsId: '9c54f3a6-d28e-4f8f-97a3-c8e939dcc8ff', url: 'https://github.com/ramu0709/new-maven-web-application.git'
 }
  
 stage('Build')
 {
  sh "${mavenHome}/bin/mvn clean package"
 }
 
 stage('ExecuteSonarQubeReport')
 {
  sh "${mavenHome}/bin/mvn sonar:sonar"
 }
 
 stage('UploadArtifactintoNexus')
 {
  sh "${mavenHome}/bin/mvn deploy"
 }
 
 stage('DeployAppIntoTomcatServer') 
 {
  sh 'cp target/maven-web-application.war /opt/tomcat/webapps/'
 }
/* 
  stage('SendEmailNotification')
 {
 emailext body: '''Build Over - Scripted way

Regards 
Batman''', subject: 'Build Over - Scripted way', to: '*****@gmail.com'
 
 }
*/ 
}
