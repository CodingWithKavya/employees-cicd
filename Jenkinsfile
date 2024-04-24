pipeline {
    agent any

    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/CodingWithKavya/employees-cicd.git'
            }
        }
        stage('SonarCloud analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonarcloud', installationName: 'SonarCloud') {
                    bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar'
                }
            }
        }
    }
}
