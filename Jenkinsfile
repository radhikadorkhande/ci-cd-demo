pipeline {
    agent any

    environment {
        TARGET_DIR = "/tmp/deploy"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    try {
                        if (fileExists('package.json')) {
                            sh 'npm install'
                        } else if (fileExists("pom.xml")) {
                            sh 'mvn package -DskipTests'
                        }
                    } catch (e) {git push -u origin main

                        error "Build Failed"
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        if (fileExists('package.json')) {
                            sh 'npm test'
                        } else if (fileExists("pom.xml")) {
                            sh 'mvn test'
                        }
                    } catch (e) {
                        error "Tests Failed"
                    }
                }
            }
            post {
                always {
                    junit '**/test-results/*.xml, **/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                sh 'tar -czf artifact.tar.gz .'
            }
        }

        stage('Deploy') {
            steps {
                sh 'mkdir -p $TARGET_DIR'
                sh 'cp artifact.tar.gz $TARGET_DIR/'
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'artifact.tar.gz'
        }
        failure {
            mail to: 'team@example.com',
                 subject: "Build Failed",
                 body: "Check Jenkins Job"
        }
        always {
            cleanWs()
        }
    }
}
