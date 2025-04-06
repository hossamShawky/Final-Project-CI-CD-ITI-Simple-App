pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = 'docker-hub'
        // IMAGE_NAME = "hossam23/jenkins-argo-nginx:${env.GIT_COMMIT}"
        
        IMAGE_NAME = "hossam23/jenkins-argo-nginx:${env.BUILD_NUMBER}"
    }

    stages {

        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        echo 'Logged in to Docker Hub ✅'
                    }
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    def app = docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Install Trivy') {
            steps {
                script {
                    // Install Trivy in the pipeline if it's not available
                    sh 'curl -sSL https://github.com/aquasecurity/trivy/releases/download/v0.22.0/trivy_0.22.0_Linux-64bit.deb -o trivy.deb'
                    sh 'sudo dpkg -i trivy.deb'
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                script {
                    echo "Running Trivy Scan 🛡️"
                    
                    // Run Trivy scan with exit code 1 on high/critical vulnerabilities
                    sh "trivy image --exit-code 1 --severity HIGH,CRITICAL ${IMAGE_NAME}"
                    
                    echo "Trivy scan completed ✅"
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        def app = docker.image("${IMAGE_NAME}")
                        app.push('latest')
                        echo "Docker Image Was Pushed ✅"
                    }
                }
            }
        }
    }
}
