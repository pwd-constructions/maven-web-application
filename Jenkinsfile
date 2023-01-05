node {

def mavenHome = tool name: "maven3.8.5"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

try{
sendslackNotifications('STARTED')

stage('checkoutcode')
{
git branch: 'development', credentialsId: '11918f57-51f2-47ff-b1b6-07ec7d7a0201', url: 'https://github.com/pwd-constructions/maven-web-application.git'
}

stage('build')
{
sh "${mavenHome}/bin/mvn clean package"
}

stage('ExecuteSonarQubeReport')
{
sh "${mavenHome}/bin/mvn sonar:sonar"
}
stage('TransferArtifactsintoNexus')
{
sh "${mavenHome}/bin/mvn deploy"
}
stage('DeployApplicationIntoTomcat')
{
sshagent(['24434070-97b1-4661-b4f5-bbe6d6cd4b66']) {
  sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.41.121:/opt/apache-tomcat-9.0.70/webapps"
}
}
}catch (e) {
  currentBuild.result= "FAILED"
  throw e
}
finally{
sendslackNotifications(currentBuild.result)
}
}

def sendslackNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: 'devopsclass2')
}

