pipeline {
    agent any
    tools { 
        maven 'Maven 3.6.3' 
        jdk 'jdk113'
    }
//    final FULL_BUILD = true;
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
    
//    stage('Code Quality Check via SonarQube') {
//     steps {
//         script 
//         {
//                 def scannerHome = tool 'sonarqube-scanner';
//                 withSonarQubeEnv("sonarqube-container") {
//                 sh "${tool("sonarqube-scanner")}/bin/sonar-scanner \
//                 -Dsonar.projectKey=test-node-js \
//                 -Dsonar.sources=. \
//                 -Dsonar.css.node=. \
//                 -Dsonar.host.url=http://127.0.0.1:9000 \
//                 -Dsonar.login=fa6ee6f740eb31d8c58fa21c07f8da2ae0580e56"
//                 }
//             }
//         }
//     }

        stage ('Validate') {
            steps {
                sh 'mvn -U clean validate'
            }
        }
        stage ('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage("Code Inspection") {
        
            steps {
                withSonarQubeEnv("sonarqube-container") 
                {
                    sh 'mvn sonar:sonar -DskipTests'
                    //sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
                }
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage("Build") {
        
            steps {
                withSonarQubeEnv("sonarqube-container") {
                    //sh 'mvn clean package sonar:sonar'
                    sh 'mvn -U -B -DskipTests package'
                    // stash name: "artifact", includes: "target/*.jar"
                }
            }
        }
        // stage('Build-MVN') { 
        //     steps {
        //         sh 'mvn -U -B -DskipTests clean package sonar:sonar'  
        //         // withEnv( ["PATH+MAVEN=${tool mvn_version}/bin"] ) 
        //         // {
        //         //      sh "mvn clean package"
        //         //      sh 'mvn -B -DskipTests clean package' 
        //         // }
        //     }            
        // }
        stage ('Artifact Upload'){
            steps{
                script 
                {
                    // unstash 'artifact'
                    // def pom = readMavenPom file: 'pom.xml'
                    // def file = "${pom.artifactId}-${pom.version}"
                    // def jar = "target/*.jar"
                    // sh "cp pom.xml ${file}.pom"

                    def server = Artifactory.server('artifactory')
                    def rtMaven = Artifactory.newMavenBuild()
                    rtMaven.resolver server: server, releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot'
                    rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'
                    rtMaven.tool = 'Maven 3.6.3'
                    rtMaven.opts = '-Xms1024m -Xmx4096m'
                    def buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install -Dbuild.number=${BUILD_ID}'
                    server.publishBuildInfo buildInfo
                }
            }
        }

        stage('Approval') {  
           steps{
                timeout(time:3, unit:'DAYS') {
                    input 'Do I have your approval for deployment?'
                }
           }
        }

        stage('Deploy') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }
}