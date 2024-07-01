pipeline {
    agent any
    tools {
        maven 'maven-3.8.6'
    }

    environment {
        repository = "192.168.222.200:8083"
        imagename = "petclinicapp"
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
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        } 
                    } 
                } 
            } 
        } 
        stage('Maven Package') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            } 
        }
        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owasp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            } 
        }
        stage('Building the Image Docker') {
            steps {
                script {
                    sh "docker build -t ${repository}/${imagename}:${BUILD_NUMBER} ."
                } 
            } 
        } 
        stage('Uploading to Nexus') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'jenkins-nexus-token', passwordVariable: 'PSW', usernameVariable: 'USER')]) {
                        sh "echo ${PSW} | docker login -u ${USER} --password-stdin ${repository}"
                        sh "docker push ${repository}/${imagename}:${BUILD_NUMBER}"
                    } 
                } 
            } 
        } 
    }
}

