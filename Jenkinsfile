pipeline {
  agent any
  
  stages {
    stage ('Checkout code') {
      steps {
              checkout scm
      }
    }
    stage('Verify tooling') {
      steps{
        sh ''' 
          java -version
          maven -version
        '''
      }
    }
    stage ('Build') {
      steps {
        bat "mvn clean install -Dmaven.test.skip=true"
      }
    }
    stage ('Archive Artifact') {
      steps {
        archiveArtifacts artifacts: 'target/*.war'
      }
    }
    stage ('Deployment') {
      steps {
        script {
          deploy adapters: [tomcat9(credentialsId: 'tomcatCred', path: '', url: 'http://:8080')], contextPath: '/pipeline', onFailure: false, war: 'webapp/target/*.war' 
        }
      }
    }
  }
}
