pipeline {
    agent any
    tools {
        maven 'M3'
    }
    stages {

        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }

        stage('Build MVN') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }

    }
}