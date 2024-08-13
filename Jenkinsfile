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
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
  
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
        stage('Build & Push Docker Image') {
            steps {
                script {
                    container('docker') {
                        docker.withRegistry('https://index.docker.io/v1/', "$DOCKER_PASS") {
                            docker_image = docker.build("${IMAGE_NAME}")
                            docker_image.push("${IMAGE_TAG}")
                            docker_image.push('latest')
                        }
                    }
                }
            }
        }
    //    stage("Trivy Scan") {
     //       steps {
       //         script {
	//	   sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ${IMAGE_NAME}:${IMAGE_TAG} --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
          //      }
          //  }

    //    }
        stage ('Cleanup Artifacts') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v -k --user admin:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://172.27.22.181:32000/job/cicd-pipeline/buildWithParameters?token=gitops-token'"
                }
            }

        }

    }
}  
