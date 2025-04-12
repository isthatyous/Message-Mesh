@Library('shared-library')_
pipeline {
    agent { label "dev" };

    environment {
        IMAGE_NAME = "two-tier-flask-app"
    }

    stages {
        stage("Code  Clone") {
            steps {
                script{
                    clone("https://github.com/isthatyous/two-tier-flask-app.git","master")
                }
            }
        }
         stage("Trivy local File Scan") {
            steps {
                script{
                    trivy_filesystem()
                }
            }
        }
        
        stage("Build") {
            steps {
                script{
                    build(env.IMAGE_NAME)
                }
            }
        }

        stage("Test") {
            steps {
                 script{
                    test()
                }
            }
        }
        stage("Trivy Docker Image Scan"){
            steps {
                 script{
                    trivy_docker_image(env.IMAGE_NAME)
                }
            }
        }
        stage("Push to Docker Hub") {
            steps {
                    script {
                        push('dockerhub-Creds',IMAGE_NAME)
                    }
                }
            }
        
        stage("Deploy") {
            steps {
                script {
                        deploy()
                }
            }
        }
    }
    
    post {
        success {
            script {
                emailext (
                    attachmentsPattern: 'file-system-trivy-report.txt , docker-image-trivy-report.txt',
                    from: 'shivamsingh22188@gmail.com',
                    to: 'shivamsingh22188@gmail.com',
                    subject: 'Build Success',
                    body: """\
Hello Shivam,

Your Jenkins build was successful! 🎉

Project: Two-Tier Flask App
Build Number: ${env.BUILD_NUMBER}
Build URL: ${env.BUILD_URL}

✔️ Code cloned
✔️ Docker image built and pushed
✔️ Deployed successfully

Cheers,
Jenkins
"""
              )      
                
            }
        }

        failure {
            script {
                emailext(
                    from: 'shivamsingh22188@gmail.com',
                    to: 'shivamsingh22188@gmail.com',
                    subject: 'Build Failure',
                    body: """\
Hello Shivam,

Your Jenkins build has failed.

Project: Two-Tier Flask App
Build Number: ${env.BUILD_NUMBER}
Build URL: ${env.BUILD_URL}

Please check the logs and take necessary action.

Regards,
Jenkins
"""
                )
            }
        }
    }
}

