node
{
    stage('Repo Replication')
    {
       git branch: 'main', url: 'https://github.com/vikasedu-github/junitreportgeneration'
    }
    
    stage('Maven Build')
    {
        def mavenHome = tool name: "Maven", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
    }
    
    stage('Unit Test with Junit')
    {
        junit 'target/surefire-reports/**/*.xml'
    }
    
    stage('Review Code')
    {
      withSonarQubeEnv(credentialsId: 'SonarJenkinsToken') 
      {
        def mavenHome = tool name: "Maven", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} sonar:sonar"
      }
    }
    
    stage('Uploading Artifact')
    {
        nexusArtifactUploader artifacts: [[artifactId: 'junitreportgeneration', classifier: '', file: 'target/junitreportgeneration-0.0.1-SNAPSHOT.jar', type: 'jar']], credentialsId: 'NexusJenkinsCred', groupId: 'com.junitreportgeneration', nexusUrl: '20.235.40.109:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'junitreportgeneration', version: '1.5-SNAPSHOT'
    }
    
    stage('Deploying on Server')
    {
        sshagent(['TomcatSSHKey'])
        {
           sh 'scp -o StrictHostKeyChecking=no target/junitreportgeneration-0.0.1-SNAPSHOT.jar vikas@20.204.145.104:/opt/tomcat/webapps/'
        }
    }
}