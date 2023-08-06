pipeline{
    agent any
    environment{
        DOCKERHUB_USERNAME = "kbagwari035"
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "$BUILD_NUMBER"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }
    stages{
        stage('Clean Workspace'){
            steps{
                script{
                    cleanWs()
                }
            }
                
        }
        stage('Git Code Clone'){
            steps{
                script{
                    git credentialsId: 'github',
                    url: 'https://github.com/kbagwari035/CodeDeployment-Using-JenkinsCI-Argo-CD-K8s.git',
                    branch: 'main'
                }
            }
        }
        stage('Build Docker Image'){
            steps{
                script{
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('',REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')

                    }
                }
            }
        }
        stage('Delete Docker images'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Updating Kubernetes deployment file'){
            steps{
                script{
                    sh """
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml
                    """
                }
            }
        }
        stage('Push the change of deployment.yml file to Git'){
            steps{
                script{
                    sh"""
                    git checkout testing
                    git config --global user.name "kul"
                    git config --global user.email "kbagwari@hotmail.com"
                    git add .
                    git commit -m "$BUILD_NUMBER"
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/kbagwari035/CodeDeployment-Using-JenkinsCI-Argo-CD-K8s.git testing"
                    }
                    
                }
            }
        }
    }   
}
