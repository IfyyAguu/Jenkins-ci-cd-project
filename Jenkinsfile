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
        post {
            always {
                junit 'target/test-reports/*.xml'
            }
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
          deploy adapters: [tomcat9(credentialsId: 'tomcatCred', path: '', url: 'http://3.8.119.115:8080')], contextPath: 'app', onFailure: false, war: 'webapp/target/*.war' 
        }
      }
    }
  }
}
