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
                    echo "Incremnet version for app"
                    sh 'mvn build-helper:parse-version versions:set \
                    -DnewVersion=\\\${parsedVersion.nextMajorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.incrementalVersion} versions:commit'
                    def matcher = readFile('pom.xml') =~ ' <version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                }
            }
        }
        stage("build jar") {
            steps {
                script {
                    echo "building the application..."
                    sh 'mvn clean package'

                }
            }
        }
        stage("build and push image") {
            steps {
                script {
                    sh "docker build -t mohmawoed/demo-app:${IMAGE_NAME} ."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                     }
                    sh "docker push mohmawoed/demo-app:${IMAGE_NAME}"
                }
            }
        }
    }
}
