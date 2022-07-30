node
{
echo"the job name is : ${env.JOB_NAME}"    
def mavenHome = tool name: "maven3.8.6"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

try{    
    
    
stage('CheckoutCode'){
git branch: 'development', credentialsId: '875a1968-c2b2-4351-a2af-bad688a21892', url: 'https://github.com/abhilashnm/maven-web-application.git'
}

stage('Build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage('executeSonarqubeReport'){
sh "${mavenHome}/bin/mvn sonar:sonar"
}

stage('uploadArtifactintoNexus'){
sh "${mavenHome}/bin/mvn deploy"
}

stage('DeployAppIntoTomcatServer'){
sshagent(['fbcca702-dbc8-43cf-a00a-fd493677b733']) {
sh "scp -o  StrictHostKeyChecking=no target/maven-web-application.war ec2-user@3.110.190.154:/opt/apache-tomcat-9.0.64/webapps/"
}
}


}
catch(e){    
currentBuild.result = "FAILURE"
throw e    
} 
finallay{
slacknotifications(currentBuild.result)
}    
    
}// node closing


def notifyBuild(String buildStatus = 'STARTED') {
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
  slackSend (color: colorCode, message: summary)
}

