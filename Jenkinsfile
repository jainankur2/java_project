pipeline {
      agent any
    stages{
        stage('Build artifact'){
            steps{
               sh label: 'compile-hello-world', script: 'mvn -B -f pom.xml clean install'
            }
        }
          
        stage('Build Image'){
            steps{
               sh 'docker build -t jainankur2/loylogic:${BUILD_NUMBER} .'
            }
        }
           
        stage('Publish Image'){
            steps{
               withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubPwd')]) {
                  sh 'docker login --username jainankur2 --password ${dockerhubPwd}'
                }
               sh 'docker push jainankur2/loylogic:${BUILD_NUMBER}'
            }
            post{
               success{
                  sh 'docker rmi -f jainankur2/loylogic:${BUILD_NUMBER}'
               }
            }
        }

        stage('Copy files on Ansible server'){
            steps{
               sshagent(credentials : ['ansible-pem']) {
                  sh 'scp ansible/deploy.yml ubuntu@172.20.11.31:/home/ubuntu/'
                  sh 'scp ansible/inventory ubuntu@172.20.11.31:/home/ubuntu/'
               }
            }
        }

        stage('Deploy Image with Ansible'){
            steps{
               sshagent(credentials : ['ansible-pem']) {
                  sh 'ssh -t -t ubuntu@172.20.11.31 -o StrictHostKeyChecking=no "ansible-playbook /home/ubuntu/deploy.yml -i /home/ubuntu/inventory -u ubuntu --private-key=/home/ubuntu/keys.pem --extra-var build_number=${BUILD_NUMBER}"'
               }
            }
        }

    }
}
