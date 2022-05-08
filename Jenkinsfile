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

      stage("SSH Into k8s Server") {
        def remote = [:]
        remote.name = 'kube-master'
        remote.host = '192.168.1.2'
        remote.user = 'ubuntu'
        remote.password = 'password'
        remote.allowAnyHosts = true

        stage('Put hellowhale.yml onto kube-master') {
            sshPut remote: remote, from: 'hellowhale.yml', into: '.'
        }

        stage('Deploy App') {
          sshCommand remote: remote, command: "kubectl apply -f hellowhale.yml"
        }
       }

     }

}
