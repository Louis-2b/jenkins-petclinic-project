pipeline {
    agent any
    tools {
        maven 'maven-3.8.6'
    }
    environment {
        imagename = "myphpapp"
        repository = "192.168.222.200:8083"
    }
    stages {
        stage('Checkout the project') {
            steps {
                git branch: 'main', url: 'https://github.com/Louis-2b/jenkins-petclinic-project.git'
            }
        }
        stage('Build the package') {
            steps {
                dir('springboot-maven-micro') {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Sonar Quality Check') {
            steps {
                script {
                    dir('springboot-maven-micro') {
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
        }
        stage('Building the Image Docker') {
            steps {
                script {
                    dir('springboot-maven-micro') {
                        sh "docker build -t ${repository}/${imagename}:${BUILD_NUMBER} ."
                    }
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
        stage("TRIVY") {
            steps {
                script {
                    sh " trivy image ${repository}/${imagename}:${BUILD_NUMBER}"
                }
            }
        }
    }
}
