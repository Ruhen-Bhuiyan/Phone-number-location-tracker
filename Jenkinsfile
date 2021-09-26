node {
def mvnHome = tool 'Maven3'

stage ('Checkout') {

checkout scm
}

stage ('Build') {
sh "${mvnHome}/bin/mvn clean install -f MyWebApp/pom.xml"
}


stage ('Code quality scan') {
withSonarQubeEnv('SonarQube') {
sh "${mvnHome}/bin/mvn sonar:sonar -f MyWebApp/pom.xml"
   }
}


    stage ('Nexus upload')
    {
        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: 'ec2-18-223-182-14.us-east-2.compute.amazonaws.com:8081',
        groupId: 'myGroupId',
        version: '1.0-SNAPSHOT',
        repository: 'maven-snapshots',
        credentialsId: '2c293828-9509-49b4-a6e7-77f3ceae7b39',
        artifacts: [
            [artifactId: 'MyWebApp',
             classifier: '',
             file: 'MyWebApp/target/MyWebApp.war',
             type: 'war']
        ]
     )
    }


stage ('DEV Deploy') {
echo "deploying to DEV tomcat "
sh 'sudo cp /var/lib/jenkins/workspace/$JOB_NAME/MyWebApp/target/MyWebApp.war /var/lib/tomcat8/webapps'
}
stage ('DEV Approve') {
echo "Taking approval from DEV"
timeout(time: 7, unit: 'DAYS') {
input message: 'Do you want to deploy?', submitter: 'admin'
     }

 }
 stage ('Slack notification') {


    slackSend(channel:'channel-name', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")


  }

}
