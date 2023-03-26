#!/usr/bin/env groovy

@Library('jenkins-shared-library')_

pipeline {
    agent any
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
        stage('Commit Version Update') {
            steps {
                script {
                    commitAndPushVersionUpdate('github.com/fsiegrist/devops-bootcamp-node-project.git', 'GitHub', 'shared-library')
                }
            }
        }
    }
}