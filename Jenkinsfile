pipeline{
    agent {
        kubernetes {
            inheritFrom "kube-agent"
        }
    }
    tools {
        jdk 'java17'
        maven 'maven3'
    }
    environment {
        APP_NAME = "cicd-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "nikhilsg"
        DOCKER_PASS = "dockerhub"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages{
        stage("cleanup workspace"){
            steps{
                cleanWs()
            }
         
        } 
        stage("checkout from scm"){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Nikhil007nsg/cicd-pipeline'
                
            } 
        }
        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }
        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }
        stage("sonarqube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId:'jenkins-sonarqube') {
                       sh "mvn sonar:sonar" 
                    }
                }
                
            }   
        }
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube'
                }
            }

        }
        stage("Build & push Docker image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }

        }

    }
}  