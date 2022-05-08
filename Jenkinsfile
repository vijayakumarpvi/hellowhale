pipeline {

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git url:'https://github.com/vijayakumarpvi/hellowhale.git', branch:'master'
      }
    }
    
      stage("Build image") {
            steps {
                script {
                    myapp = docker.build("vijayakumarpvi/hellowhale:${env.BUILD_ID}")
                }
            }
        }
    
      stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

    
      stage('Deploy App') {
            steps {
                container('kubectl') {
                    withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
		                   sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" 'hellowhale.yml'
			                 sh 'kubectl apply -f hellowhale.yml'
                    }
                }
            }
         }

     }

}
