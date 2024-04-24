node {
  stage('SCM') {
    git 'https://github.com/CodingWithKavya/health-api.git'
  }
  stage('SonarCloud analysis') {
    withSonarQubeEnv(credentialsId: 'sonarcloud', installationName: 'SonarCloud') { // You can override the credential to be used
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar'
    }
  }
}
