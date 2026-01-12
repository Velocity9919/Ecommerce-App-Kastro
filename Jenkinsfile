pipeline {
    agent any
    tools {
	    jdk 'jdk 17'
        maven "maven 3.9.12"
    }
	environment {
        SCANNER_HOME=tool 'sonar-scanner'
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
		stage('File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=ECommerce \
                    -Dsonar.projectKey=ECommerce \
                    -Dsonar.java.binaries=target/classes
                    '''
                }
            }
        }
    }
}
