node
{
echo"the job name is : ${env.JOB_NAME}"    
def mavenHome = tool name: "maven3.8.6"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

    
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
sh "scp -o  StrictHostKeyChecking=no target/maven-web-application.war ec2-user@52.66.210.124:/opt/apache-tomcat-9.0.64/webapps/"
}
}



}
