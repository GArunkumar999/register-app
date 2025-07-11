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
         JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
         IMAGE_TAG = "${BUILD_NUMBER}"
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
        stage("Trigger CD Pipeline") {
                steps {
                    script {
                        sh "curl -v -k --user admin:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://ec2-54-90-71-191.compute-1.amazonaws.com:8080/job/register-app-cd/buildWithParameters?token=gitops-token'"
                    }
                }
        }
    }
    post {
        success {
           echo "CI pipeline completed. CD pipeline triggered successfully."
        }
        failure {
           echo "CI pipeline failed. Check logs for errors."
        }
    }



    
}
