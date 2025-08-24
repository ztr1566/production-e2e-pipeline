pipeline {
    agent {
        label 'ubuntu-agent'
    }
    tools {
        jdk 'Java-21'
        maven 'Maven-4'
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
    }
}
