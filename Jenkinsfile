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
        stage ('Deploy') {
            agent {
                label 'Deployment-server'
            }
            steps {
                echo "Deploying"
                unstash 'war-file'
                sshagent(credentials: ['Jenkins-main']) {
                    sh 'scp -o StrictHostKeyChecking=no target/*.war centos@172.31.46.162:~/apache*/webapps/'
                }
            }
        }
    }
    post {
        success {
            // Send email notification on successful build
             build.result.toString().equals('SUCCESS')
            emailext (
                to: 'ifeomaagu43@gmail.com',
                subject: "Jenkins Pipeline - Build Successful",
                body: "Your Jenkins pipeline for Java Calculator build, test and deploy was successful. View console output: ${env.BUILD_URL}",
                attachLog: true
            )
        }
        failure {
            // Send email notification on failed build
            build.result.toString().equals('FAILURE')
            emailext (
                to: 'ifeomaagu43@gmail.com',
                subject: "Jenkins Pipeline - Build Failed",
                body: "Your Jenkins pipeline for Java Calculator build, test and deploy failed. View console output: ${env.BUILD_URL}",
                attachLog: true
            )
        }
    }
}
