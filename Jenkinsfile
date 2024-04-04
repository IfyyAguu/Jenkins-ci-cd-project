pipeline {
    agent {
        label 'BuildTest-server'
    }
    stages {
        stage ('Checkout code') {
            steps {
                echo "Checking out source code"
                checkout scm
            }
        }
        stage('Verify tooling') {
            steps {
                echo "packages"
                sh ''' 
                    java -version
                    mvn -version
                '''
            }
        }
        stage ('Build') {
            steps {
                echo "Building"
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                echo "Testing"
                sh 'mvn test'
                stash name: 'war-file', includes: 'target/*.war'
            }
        }
        stage ('Archive Artifact') {
            steps {
                echo "Archiving"
                archiveArtifacts artifacts: 'target/*.war'
            }
        }
        stage ('Deploy') {
            agent {
                label 'Deployment-server'
            }
            steps {
                echo "Deploying"
                unstash 'war-file'
                sshagent(credentials: ['Jenkins-main']) {
                    sh 'scp -o StrictHostKeyChecking=no target/*.war centos@172.31.46.162:~/'
                }
            }
        }
    }
}

