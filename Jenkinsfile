pipeline {
    agent {
        label 'ubuntu-agent'
    }
    tools {
        jdk 'Java-21'
        maven 'Maven-4'
    }
    environment {
        APP_NAME = 'production-e2e-pipeline'
        RELEASE_VERSION = '1.0.0'
        DOCKER_USER = 'zizoo1566'
        DOCKER_PASSWORD = credentials('docker-credentials')
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE_VERSION}-${env.BUILD_NUMBER}"
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout SCM') {
            steps {
                git(
                    branch: 'main',
                    credentialsId: 'github-credentials',
                    url: 'https://github.com/ztr1566/production-e2e-pipeline.git'
                )
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test Application') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonarqube-token') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-credentials') {
                        sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
                        sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
                        sh 'docker push ${IMAGE_NAME}:latest'
                    }
                }
            }
        }

    }
}
