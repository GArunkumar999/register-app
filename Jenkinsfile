pipeline{
    agent{
        label 'agent-1'
    }
    tools{
        maven 'Maven3'
        jdk 'Java17'
    }
    environment{
         APP_NAME = "register-app-pipeline"
    }
    parameters{
        string(name: 'IMAGE_TAG', description: 'Who should I say hello to?')
    }
    stages{
        stage('cleanup Workspace'){
            steps{
                cleanWs()
            }
        }
        stage('checkout from scm'){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/GArunkumar999/register-app.git'
            }

        }
        stage('build application'){
            steps{
                sh "mvn clean package"
            }
        }
        stage('test application'){
            steps{
                sh "mvn test"
            }
        }
        stage('build image'){
            steps {
               withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                 sh """
                    docker build -t arun596/registerapp:${IMAGE_TAG} .
                    echo "\$DOCKER_PASS" | docker login -u "\$DOCKER_USER" --password-stdin
                    docker push arun596/registerapp:${IMAGE_TAG}
                    
                  """
                }
            }

            
        }
        stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image arun596/registerapp:${IMAGE_TAG} --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }

       stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi arun596/registerapp:${IMAGE_TAG}"
               }
          }
       }


    }
}
