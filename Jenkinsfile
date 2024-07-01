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
                sh 'mvn clean package'
            } 
        }
        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owasp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            } 
        }
    }
}

