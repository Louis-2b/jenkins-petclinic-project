pipeline {
    agent any
    tools {
        maven 'maven-3.8.6'
    }

    stages {
        stage('Checkout the project') {
            steps {
                git branch: 'test', url: 'https://github.com/Louis-2b/jenkins-petclinic-project.git'
            }
        }
        stage('Maven Compile') {
            steps {
                sh 'mvn clean compile'
            } 
        } 
        stage("Maven Test") {
            steps {
                sh 'mvn test'
            } 
        } 
        stage('Sonar Quality Check') {
            steps {
                script {
                    withSonarQubeEnv(installationName: 'sonar-latest',credentialsId: 'jenkins-sonar-token') {
                        sh 'mvn sonar:sonar'
                    } 
                } 
            } 
        } 
    }
}

