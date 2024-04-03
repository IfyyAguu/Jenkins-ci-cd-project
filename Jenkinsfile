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
          mvn -version
        '''
      }
    }
    stage ('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }
    stage('Test') {
      steps {
            sh 'mvn test'
        }
    }
    stage ('Archive Artifact') {
      steps {
        archiveArtifacts artifacts: 'target/*.war'
      }
    }
    stage ('Deploy') {
      steps {
        script {
          deploy adapters: [tomcat7(credentialsId: 'tomcatCred', path: '', url: 'http://13.40.184.14:8080')], contextPath: 'app', war: 'target/*.war' 
        }
      }
    }
  }
}
