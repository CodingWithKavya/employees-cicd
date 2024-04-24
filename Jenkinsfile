pipeline {
    agent any
    tools {
        // Specify the correct Maven installation name
        maven 'maven'
    }

node {
  stage('SCM') {
    git 'https://github.com/CodingWithKavya/employees-cicd.git'
  }
  stage('SonarCloud analysis') {
    withSonarQubeEnv(credentialsId: '3dd007892cc9e1261a32285ee54f82ab97518f15', installationName: 'SonarCloud') { // You can override the credential to be used
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar'
    }
  }
}
}
