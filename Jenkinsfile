def projectKey = 'codingwithkavya' // Replace with your SonarCloud project key
def branch = env.BRANCH_NAME ?: 'main' // Use branch name from environment or default to 'master'
def gitUrl = 'https://github.com/CodingWithKavya/employees-cicd.git' // Replace with your Git repository URL

pipeline {
  agent any
  tools {
        maven 'maven'
    }

  stages {
    stage('Checkout Code') {
      steps {
        bat "git checkout ${branch} && git pull origin ${branch}" // Use bat for Windows commands
      }
    }
    
    stage('Code Analysis') {
      steps {
        script {
          // Define SonarCloud server URL and token as Jenkins job environment variables
          environment {
            SONAR_HOST_URL = 'https://sonarcloud.io'
            SONAR_TOKEN = '$sonarcloud' // Reference token from credential
          }

          // Define additional SonarCloud properties (optional)
          bat """
          echo 'sonar.sources=src/main/java' // Replace with path to your source code
          """
          
          // Run SonarCloud analysis using powershell
          bat 'powershell "mvn sonar:sonar"'
        }
      }
    }
  }
}
