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
    }
}

