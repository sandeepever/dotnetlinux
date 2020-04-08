pipeline{
    agent any
    stages{
        stage('Git Checkout'){
            steps{
                git 'https://github.com/sandeepever/dotnetlinux'  
            }
        }

        stage('Build Docker Images'){
            steps{
                sh 'docker build -t sandeep76/dotnetlinux .'
            }
        }
         
        stage('Push to Docker Hub'){ 
            steps{
                withCredentials([string(credentialsId: 'docker-pwd', variable: 'dockerhubCred')]) {
                     sh "docker login -u sandeep76 -p ${dockerhubCred}"
                }
                     sh 'docker push sandeep76/dotnetlinux'
            }
        }

        /*stage('Remove Previous Container'){
            steps{
                script{
                    try{
                        sh 'docker rm -f dotnetlinuxp'             
                    }catch(error){
                        sh 'docker stop dotnetlinux'
                        sh 'docker rm dotnetlinux'            
                    }
                }
            }
        }*/

        /*stage('Deploy to Dev Environment'){
            sh 'docker run -d -p 9000:80 --name webapp sandeep76/dotnetlinux'
        }*/
 
        stage('Deploy on k8s cluster'){
            steps{
                 /*kubernetesDeploy(
                    configs: 'kubedeploy.yaml',
                    kubeconfigId: 'KUBERNETES_CICD',
                    enableConfigSubstitution: true
                )*/
                sshagent(['KubeSSH']) {
                    sh "scp -o StrictHostKeyChecking=no myservice.yaml mydeploy.yaml sandeep@192.168.122.2:/home/sandeep/"
                    script{
                        try{
                            sh "ssh sandeep@192.168.122.2 kubectl apply -f ."
                        }catch(error){
                            sh "ssh sandeep@192.168.122.2 kubectl create -f ."
                        }
                    }
                }
            } 
        } 
    }
}


