node {
  stage('SCM') {
    git 'https://github.com/CodingWithKavya/health-api.git'
  }
  stage('SonarCloud analysis') {
    withSonarQubeEnv(credentialsId: '43a22d443d4fba4bec7197773d5a777ec82f709d', installationName: 'SonarCloud') { // You can override the credential to be used
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar'
    }
  }
}
