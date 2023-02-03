pipeline{

    agent any

    stages{

        stage('Git checkout'){

            steps{
                 git branch: 'main', url: 'https://github.com/qwerpoi123/demo-counter-app.git'
            }

        }
        stage('UNIT Testing'){

            steps{
                 sh 'mvn test'
            }

        }
        stage('Integration testing'){

            steps{
                sh 'mvn verify -DskiUnitTests'
            }

        }
        stage('maven build'){

            steps{
                sh 'mvn clean install'
            }
        }
        stage('SonarQube analysis'){

            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-api'){
                    sh 'mvn clean package sonar:sonar'
                 }
                }
            }
           
        }
        stage('Quality gate status'){

            steps{

                script{

                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                }
            }
        }
        stage('upload warfile to nexus'){

            steps{

                script{

                    def readPomVersion = readMavenPom file: 'pom.xml'

                    def nexusRepo = readPomVersion.version.endsWith{"SNAPSHOT"} ? "demoapp-snapshot" : "demoapp-release"
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', file: 'target/Uber.jar',
                            type: 'jar'
                            ]
                    ],
                    credentialsId: 'nexus-auth',
                    groupId: 'com.example',
                    nexusUrl: '34.233.123.181:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: nexusRepo,
                    version: "${readPomVersion.version}"
                }
            }
        }
    }
}