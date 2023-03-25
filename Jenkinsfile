#!/usr/bin/env groovy

pipeline {
    agent any
    stages {
        stage('Bump Version') {
            steps {
                script {
                    echo 'incrementing patch version...'
                    dir('app') {
                        sh 'npm version patch'

                        def packageJson = readJSON file: 'package.json'
                        def version = packageJson.version

                        env.IMAGE_VERSION = "$version-$BUILD_NUMBER"
                    }
                }
            }
        }
        stage('Run Tests') {
            steps {
                script {
                    dir('app') {
                        sh 'npm install'
                        sh 'npm run test'
                    } 
                }
            }
        }
        stage('Build and Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
                    sh "docker build -t fsiegrist/fesi-repo:devops-bootcamp-node-project-${IMAGE_VERSION} ."
                    sh "echo ${PASSWORD} | docker login -u ${USERNAME} --password-stdin"
                    sh "docker push fsiegrist/fesi-repo:devops-bootcamp-node-project-${IMAGE_VERSION}"
                }
            }
        }
        stage('Commit Version Update') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'GitHub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh 'git config user.email "jenkins@example.com"'
                        sh 'git config user.name "jenkins"'

                        sh "git remote set-url origin https://${USER}:${PWD}@github.com/fsiegrist/devops-bootcamp-node-project.git"
                        sh 'git add app/package.json'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:main'

                        sh 'git config --unset user.email'
                        sh 'git config --unset user.name'
                    }
                }
            }
        }
    }
}