pipeline {
    agent any
    tools {
	    jdk 'jdk 17'
        maven "maven 3.9.12"
    }
	environment {
        SCANNER_HOME=tool 'sonar-scanner'
        DOCKER_IMAGE = "nareshbabu1991/ecommerce:latest"
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

        stage('Maven Build') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }

        stage('Publish to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven-setting', jdk: 'jdk 17', maven: 'maven 3.9.12') {
                    sh "mvn deploy -DskipTests=true"
                }
            }
        }

        stage('Docker Build & Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker build -t ${DOCKER_IMAGE} ."
                    }
                }
            }
        }

        stage('Docker Image Scan') {
            steps {
                sh "trivy image --format table -o trivy-image-report.html ${DOCKER_IMAGE}"
                archiveArtifacts artifacts: 'trivy-image-report.html', fingerprint: true
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
		
		stage('Deploy to Container') {
            steps {
                script {
                    sh "docker stop ecommerce-container || true && docker rm ecommerce-container || true"
                    sh "docker run -d --name ecommerce-container -p 8083:8080 ${DOCKER_IMAGE}"
                }
            }
        }
    }
}
