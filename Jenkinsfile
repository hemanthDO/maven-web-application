node
{
    properties([
     buildDiscarder(logRotator(numToKeepStr: '3')),
     pipelineTriggers([
         pollSCM('* * * * *')
     ])
   ])
    def mvnHome=tool name: "maven3.6.3"
    stage('CheckOutCode')
    {
        git branch: 'development', credentialsId: 'cac2dcd1-0724-486b-9515-b77ce216b678', url: 'https://github.com/hemanthDO/maven-web-application.git'
    }
    stage('Build')
    {
        sh "${mvnHome}/bin/mvn clean package"
    }
    stage('SonarQubeReport')
    {
         sh "${mvnHome}/bin/mvn sonar:sonar"
    }
	stage('UploadArtifactInTONexus')
    {
        sh "${mvnHome}/bin/mvn clean deploy"
    }
    stage('DeployeToTomcat')
    {
        sshagent(['1f4333ec-9246-47a5-bed9-e7abaca04403'])
        {
        sh  "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.235.95.141:/opt/apache-tomcat-9.0.30/webapps/" 
        }
        
    }
    stage('SendEmailNotification')
    {
        emailext body: '''$DEFAULT_CONTENT


    thanks&regards,
    Hemanth.M
    8152064416''', subject: '$DEFAULT_SUBJECT-Amazon project pipeline', to: 'm.hemanth.redhat@gmail.com'
    }
}
