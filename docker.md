pipeline {
    agent any
    environment {
     Docker_Tag = "v4"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Tangala123/hiring'
            }
        }
        stage('maven build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('Docker build') {
            steps {
                sh "docker build -t tangalalakshmi/my-docker:${Docker_Tag} ."
            }
        }
        stage('Docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'pwd', usernameVariable: 'usr')]) {
                 sh " docker login -u ${usr} -p ${pwd}"
                }
            }
        }
        stage('Docker push') {
            steps {
                sh "docker push tangalalakshmi/my-docker:${Docker_Tag} "
            }
        }
        stage('Docker deploy') {
            steps {
                sshagent(['docker-deploy']) {
                 sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.8.166 docker run -d -p 4564:8080 --name mydocker3 tangalalakshmi/my-docker:${Docker_Tag} "
                }
            }
        }
    }
}
