#!/usr/bin/env groovy

pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        GITHUB_CREDENTIALS = credentials('github-token')
        GIT_AUTHOR_NAME = 'Jenkins user account'
        GIT_AUTHOR_EMAIL = 'jenkins@example.com'
        GIT_COMMITTER_NAME = 'jenkins user account'
        GIT_COMMITTER_EMAIL =  'jenkins@example.com'
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
        stage("Commit Version update"){
            steps{
                script{
                    checkout scm
                    sh 'git add .'
                    sh 'git commit -m "Jenkins automated commit"'
             //       git credentialsId: GITHUB_CREDENTIALS, url: 'https://github.com/mohmawoed/java-maven-app.git'' 
               //     sh 'git config --global user.email "Jenkins@example.com"'
                 //   sh 'git config --global user.name "Jenkins"'
                   // sh 'git add .'
                    //sh 'git commit -m "update version"
                    //sh 'git push origin HEAD:feature/SoftVer'

                }
                
            }
        }
    }
}
