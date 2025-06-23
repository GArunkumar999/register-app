pipeline{
    agent{
        label 'agent-1'
    }
    tools{
        maven 'Maven3'
        jdk 'Java17'
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

    }
}
