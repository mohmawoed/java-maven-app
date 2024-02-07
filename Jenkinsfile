#!/usr/bin/env groovy

pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage("increment version") {
            steps {
                script {
                    echo "building the application..."
                }
            }
        }
        stage("build jar") {
            steps {
                script {
                    echo "building the application..."
                    sh 'mvn package'

                }
            }
        }
        stage("build and push image") {
            steps {
                script {
                    sh 'docker build -t mohmawoed/demo-app:jM-1.0 .'
                     script.withCredentials([script.usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        script.sh "echo $script.PASS | docker login -u $script.USER --password-stdin"
                     }
                    sh 'docker push mohmawoed/demo-app:jM-1.0'
                }
            }
        }
    }
}
