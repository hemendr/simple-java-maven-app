pipeline {
    agent any
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
                def mvn_version = 'M3'
                withEnv( ["PATH+MAVEN=${tool mvn_version}/bin"] ) 
                {
  //sh "mvn clean package"
                     sh 'mvn -B -DskipTests clean package' 
                }
            }
        }

    }
}