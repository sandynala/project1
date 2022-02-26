pipeline {
    agent any
    environment {
        dockerImage = "ashagari/javaapp_$JOB_NAME:$BUILD_NUMBER"
        dockerContainerName = 'javaapp_$JOB_NAME_$BUILD_NUMBER'
    }
    tools {
        maven 'maven'
        jdk 'jdk'
    }
    stages {
        stage('git checkout') {
            steps {
                git branch: 'master',
                url: 'https://github.com/sandynala/project1.git'
                sh 'ls -al'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -Dmaven.test.skip=true'
                sh 'ls -al'
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'SonarQube Scanner' // the name you have given the Sonar Scanner (Global Tool Configuration
            }
            steps {
                withSonarQubeEnv(installationName: 'SonarServer') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Build Docker Image ') {
            steps {
                sh "docker build  -t ${dockerImage} ."
            }
        }

        stage('Docker Run') {
            steps {
                sh "docker run -dit --name ${dockerContainerName} -p 8000:8080 ${dockerImage}"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'Dockerhub-key', variable: 'dockerPWD')]) {
                    sh "docker login -u ashagari -p ${dockerPWD}"
                }
                sh "docker push ${dockerImage}"
            }
        }
    }
}
