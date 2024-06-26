pipeline {
      agent any
    stages{
        stage('download file'){
            steps{
               sh 'wget https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.zip'
               sh 'mv apache-maven-3.6.3-bin.zip my_zip_folder/'
               withCredentials([usernamePassword(credentialsId: 'git-password', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh ('git config --global user.email "jain.ankur0592@gmail.com"')
                        sh ('git config --global user.name "Ankur Jain"')
                        sh('git add .')
                        sh('git commit -m "updating zip"')
                        sh('git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/jainankur2/java_project.git HEAD:master')
                    }
            }
        }
          
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
