pipeline {
    agent any
    tools { 
        maven 'Maven 3.6.3' 
        jdk 'jdk113'
        sonarqube 'sonarqube-scanner'

    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }

   stage('Code Quality Check via SonarQube') {
    steps {
        script 
        {
                def scannerHome = tool 'sonarqube-scanner';
                withSonarQubeEnv("sonarqube-container") {
                sh "${tool("sonarqube")}/bin/sonar-scanner \
                -Dsonar.projectKey=test-node-js \
                -Dsonar.sources=. \
                -Dsonar.css.node=. \
                -Dsonar.host.url=http://192.168.44.137/9000 \
                -Dsonar.login=fa6ee6f740eb31d8c58fa21c07f8da2ae0580e56"
                }
            }
        }
    }
        // stage('Build') {
        //     steps {
        //         echo 'Building..'
        //     }
        // }
        // stage('Test') {
        //     steps {
        //         echo 'Testing..'
        //     }
        // }

        stage('Build-MVN') { 

            // def mvn_version = 'M3'
            steps {
                sh 'mvn -B -DskipTests clean package' 
                // withEnv( ["PATH+MAVEN=${tool mvn_version}/bin"] ) 
                // {
                //     //sh "mvn clean package"
                //      sh 'mvn -B -DskipTests clean package' 
                // }
            }
            
        }

        stage('Test') 
        {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }
}