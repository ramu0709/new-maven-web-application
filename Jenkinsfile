node
{

 def mavenHome = tool name: "Maven3.9.8"

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
