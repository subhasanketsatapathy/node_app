pipeline {
    environment { 
        registry = "subhasanket/test" 
        registryCredential = 'docker_id' 
        dockerImage = '' 
    }
    agent any
    stages{
        stage('Initialize'){
            steps {
                script {
                    def dockerHome = tool 'myDocker'
                    env.PATH = "${dockerHome}/bin:${env.PATH}"
                }
            }
        }
         stage('Cloning our Git') { 
            steps { 
                git 'https://github.com/subhasanketsatapathy/node_app' 
            }
        } 
        stage('Building our image') 
        {
            steps
            {
                sh 'docker --version'
                script 
                { 
                    dockerImage = docker.build registry + ":$BUILD_NUMBER" 
                }
                sh 'docker images'
            }
        }
        stage('Push image') { 
            steps { 
                script { 
                    docker.withRegistry( '', registryCredential ) { 
                        dockerImage.push() 
                    }
                } 
            }
        } 
         stage('Pull image from hub'){
            steps{
                script{
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.pull()
                   
                }
            }
            sh  'docker images'
         }
       }
       stage('Run Image'){
           steps{
               sh ''' 
               if [ $(docker ps -qf "name=nodejs_app") ]
                then
                echo "from if block"
                docker kill nodejs_app && docker rm nodejs_app
                docker run -d -p 3456:8080 --name nodejs_app "${registry}":"${BUILD_NUMBER}"
                docker ps
               else
                echo "from else block"
                docker run -d -p 3456:8080 --name nodejs_app "${registry}":"${BUILD_NUMBER}"
                docker ps
                fi
               '''
           }
       }   
     }
}
