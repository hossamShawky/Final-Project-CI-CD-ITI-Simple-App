pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = 'docker-hub'
        IMAGE_NAME = "hossam23/jenkins-argo-nginx:${env.GIT_COMMIT}"
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

        stage('Trivy Scan') {
            steps {
                script {
                    echo "Running Trivy Scan 🛡️"

                    // Assuming Trivy is installed in your Jenkins agent
                    sh "trivy image --exit-code 1 --severity HIGH,CRITICAL ${IMAGE_NAME}" // Fail the build if high/critical vulnerabilities are found

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
