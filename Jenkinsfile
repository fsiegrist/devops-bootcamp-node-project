#!/usr/bin/env groovy

pipeline {
    agent any
    tools {
        nodejs 'node-18'
    }
    stages {
        stage("Bump Version") {
            steps {
                script {
                    echo 'incrementing patch version...'
                }
            }
        }
    }
}