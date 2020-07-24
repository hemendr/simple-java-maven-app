pipeline {
    agent any
    tools { 
        maven 'Maven 3.6.3' 
        jdk 'jdk113' 
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
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
            def mvn_version = 'M3'
            steps {
                withEnv( ["PATH+MAVEN=${tool mvn_version}/bin"] ) 
                {
                    //sh "mvn clean package"
                     sh 'mvn -B -DskipTests clean package' 
                }
            }
        }

    }
}