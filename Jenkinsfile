pipeline {
    agent any

    environment {
        DOCKER_IMAGE_OWNER = 'dangdang42'
        DOCKER_IMAGE_TAG = "v${env.BUILD_NUMBER}"
        DOCKER_PWD = credentials('dockerhub')
        GIT_CREDENTIALS = credentials('github_access_token')
        REPO_URL = 'gongbu22/project-parking-CD.git'
        COMMIT_MESSAGE = 'Update README.md via Jenkins Pipeline'
    }

    stages {
        stage('clone from SCM') {
            steps {
                sh '''
                rm -rf project-parking-yj
                git clone https://github.com/gongbu22/project-parking-yj.git
                '''
            }
        }

        stage('Docker Image Building') {
            steps {
                dir('project-parking-yj'){
                sh '''
                docker build -t ${DOCKER_IMAGE_OWNER}/msa-frontend-nginx:${DOCKER_IMAGE_TAG} -f ./msa-frontend/nginx-Dockerfile ./msa-frontend
                docker build -t ${DOCKER_IMAGE_OWNER}/msa-frontend-nodejs:${DOCKER_IMAGE_TAG} -f ./msa-frontend/nodejs-Dockerfile ./msa-frontend
                docker build -t ${DOCKER_IMAGE_OWNER}/msa-parking-service:${DOCKER_IMAGE_TAG} ./msa-parking-service
                docker build -t ${DOCKER_IMAGE_OWNER}/msa-payment-service:${DOCKER_IMAGE_TAG} ./msa-payment-service
                docker build -t ${DOCKER_IMAGE_OWNER}/msa-register-service:${DOCKER_IMAGE_TAG} ./msa-register-service
                docker build -t ${DOCKER_IMAGE_OWNER}/msa-statistics-service:${DOCKER_IMAGE_TAG} ./msa-statistics-service
                '''
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USR', passwordVariable: 'DOCKER_PWD')]) {
                sh "echo $DOCKER_PWD | docker login -u $DOCKER_USR --password-stdin"}
            }
        }

        stage('Docker Image pushing') {
            steps {
                sh '''
                docker push ${DOCKER_IMAGE_OWNER}/msa-frontend-nginx:${DOCKER_IMAGE_TAG}
                docker push ${DOCKER_IMAGE_OWNER}/msa-frontend-nodejs:${DOCKER_IMAGE_TAG}
                docker push ${DOCKER_IMAGE_OWNER}/msa-parking-service:${DOCKER_IMAGE_TAG}
                docker push ${DOCKER_IMAGE_OWNER}/msa-payment-service:${DOCKER_IMAGE_TAG}
                docker push ${DOCKER_IMAGE_OWNER}/msa-register-service:${DOCKER_IMAGE_TAG}
                docker push ${DOCKER_IMAGE_OWNER}/msa-statistics-service:${DOCKER_IMAGE_TAG}
                '''
            }
        }

        stage('Docker Logout') {
            steps {
                sh '''
                docker logout
                '''
            }
        }
    }
}
