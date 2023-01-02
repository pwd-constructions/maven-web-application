node {

def mavenHome = tool name: "maven3.8.5"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

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

}
