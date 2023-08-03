pipeline {
    agent any

    stages{
        stage("Clone Code"){
            steps {
                echo "Cloning the code"
                git url:"https://github.com/kbagwari035/App-Deploy-On-Container-Via-Ansible-CI-CD.git", branch: "main"
            }
        }
        
        stage("Build Image on Ansible "){
            steps {
                echo "Deploying the container"
                sshagent(['Ansible']) {
                sh "scp -r -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null * ansible@192.168.56.117:"
                sh "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ansible@192.168.56.117 docker build -t reddit-app ."
                }
            }
        }
        
        stage("Pushing Image "){
            steps {
                echo "Deploying the container"
                sshagent(['Ansible']) {
                withCredentials([usernamePassword(credentialsId:"dockerhub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                sh "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ansible@192.168.56.117 docker tag reddit-app ${env.dockerHubUser}/reddit-app:latest"
                sh "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ansible@192.168.56.117 docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ansible@192.168.56.117 docker push ${env.dockerHubUser}/reddit-app:latest"
                }     
             }
          }
       }
     
        stage("Testing Image  "){
            steps {
                echo "Deploying the container"
                sshagent(['Ansible']) {
                sh "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ansible@192.168.56.117 ansible-playbook ansible-deployment.yml"
                }     
             }
          }

        stage("Deploy"){
            steps {
                echo "Deploying the container"
                sshagent(['K8s']) {
                script{
                    try{
                        sh "ssh devops@192.168.56.115 kubectl apply -f deployment.yml"
                    }catch(error){
                        sh "ssh devops@192.168.56.115 kubectl apply -f deployment.yml"
                    }
                  }
                }
              }
            }
         }    
       }
