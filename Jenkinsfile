pipeline {
    agent none
    
    stages {
        stage('CLONE GIT REPOSITORY') {
            agent {
                label 'AppServer2'
            }
            steps {
                checkout scm
            }
        }  

        stage('SCA-SAST-SNYK-TEST') {
            agent {
                label 'AppServer2'
            }
            steps {
                script {
                    snykSecurity(
                      snykInstallation: 'Snyk@latest',
                      snykTokenId: 'Snykid',
                      severity: 'high'
                    )
                }
            }
        }

        stage('SonarQube Analysis') {
            agent {
                label 'Sonarqube-Server-CWEB2140'
            }
            steps {
                script {
                    def scannerHome = tool 'SonarQubeScanner'
                    withSonarQubeEnv('SonarQubeCA') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=Chat-App \
                            -Dsonar.sources=."
                    }
                }
            }
        }

        stage('BUILD-AND-TAG') {
            agent {
                label 'AppServer2'
            }
            steps {
                script {
                    def app = docker.build("noahbartell/node_chat_app")
                }
            }
        }

        stage('POST-TO-DOCKERHUB') {    
            agent {
                label 'AppServer2'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        def app = docker.build("noahbartell/node_chat_app")
                        app.push("latest")
                    }
                }
            }
        }

        stage('DEPLOYMENT') {    
            agent {
                label 'AppServer2'
            }
            steps {
                sh "docker-compose down"
                sh "docker-compose up -d"   
            }
        }
    }
}
