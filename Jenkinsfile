node('AppServer2')
{
    def app
    stage('Clone Git')
    {
        // Clone the repository
        checkout scm
    }
    stage('SCA TEST')
    {
        snykSecurity(
          snykInstallation: 'Snyk@latest',
          snykTokenId: 'Snykid',
          severity: 'high'
        )
    }
    stage('SonarQube Analysis'){
    def scannerHome = tool = 'SonarQubeScanner';
    withSonarQubeEnv('SonarQube')
        {
        sh "${scannerHome}/bin/sonar-scanner"
        }
    }
    stage('Build and tag')
    {
        app = docker.build("noahbartell/node_chat_app")
    }
    stage('Post to Docker Hub')
    {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
        {
            app.push("latest")
        }
    }
    stage('Pull and Deploy')
    {
        sh 'docker-compose down'
        sh 'docker-compose up -d'
    }
}
