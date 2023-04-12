#!/usr/bin/env groovy

@Library('jenkins-shared-library')_

pipeline {
    agent any

    parameters {
        booleanParam(name: 'deploy', defaultValue: false, description: 'Deploy the application on the EC2 server.') 
    }

    stages {
        stage('Bump Version') {
            steps {
                script {
                    bumpNpmVersion('app', 'patch')
                }
            }
        }
        stage('Run Tests') {
            steps {
                script {
                    runNpmTests('app')
                }
            }
        }
        stage('Build and Push Docker Image') {
            steps {
                buildAndPublishImage("fsiegrist/fesi-repo:devops-bootcamp-node-project-${IMAGE_VERSION}")
            }
        }
        stage('Deploy to EC2') {
            when {
                expression {
                    params.deploy
                }
            }
            steps {
                script {
                    echo 'deploying Docker image to EC2 server...'
                    
                    def dockerComposeCmd = "IMAGE_TAG=${IMAGE_TAG} docker-compose up -d"
                    def ec2Instance = "ec2-user@3.122.205.189"

                    sshagent(['ec2-server-key']) {
                        sh "scp -o StrictHostKeyChecking=no docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${dockerComposeCmd}"
                    }
                }
            }
        }
        stage('Commit Version Update') {
            steps {
                script {
                    commitAndPushVersionUpdate('github.com/fsiegrist/devops-bootcamp-node-project.git', 'GitHub')
                }
            }
        }
    }
}