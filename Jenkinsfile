pipeline {
    agent { label "dev" };

    environment {
        IMAGE_NAME = "two-tier-flask-app"
    }

    stages {
        stage("Code Clone") {
            steps {
                git url: "https://github.com/isthatyous/two-tier-flask-app.git", branch: "master"
            }
        }
         stage("Trivy  Scan") {
            steps {
                sh 'trivy fs --severity HIGH,CRITICAL . -o trivy_report.html'
                sh 'trivy convert --exit-code 1 --severity HIGH,CRITICAL trivy_report.html'
            }
        }
        
        stage("Build") {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage("Test") {
            steps {
                echo "Developer / Tester tests likh ke dega..."
            }
        }

        stage("Push to Docker Hub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-Creds',
                    usernameVariable: 'DockerHubUser',
                    passwordVariable: 'DockerHubPass'
                )]) {
                    script {
                        echo "Pushing Image to Docker Hub"
                        sh "docker image tag ${IMAGE_NAME} ${DockerHubUser}/${IMAGE_NAME}"
                        sh "docker login -u ${DockerHubUser} -p ${DockerHubPass}"
                        sh "docker push ${DockerHubUser}/${IMAGE_NAME}"
                        echo "Image Pushed to Docker Hub Successfully"
                    }
                }
            }
        }

        stage("Deploy") {
            steps {
                sh 'docker compose up -d --build flask-app'
            }
        }
    }
}
