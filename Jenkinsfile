pipeline {
    agent any
    tools {
        maven "maven 3.8.7"
    }
    stages {
        stage("Cleanup Workspace"){
            steps{
                cleanWs()
            }
        }

        stage("Git Checkout"){
            steps{
                git branch: 'master', changelog: false, poll: false, url: 'https://github.com/Velocity9919/Ecommerce-App-Kastro.git'
            }
        }

        stage("build maven"){
            steps {
                sh "mvn clean compile"
            }
        }

        stage("test"){
            steps{
                sh "mvn test"
            }
        }
    }
}
