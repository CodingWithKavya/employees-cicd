pipeline {
    agent any
    tools {
        maven 'maven'
    }

    stages {
        stage('Git Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/CodingWithKavya/employees-cicd.git']])
                echo 'Git Checkout Completed'
            }
        }
    stage('Maven Build') {
            steps {
                bat 'mvn clean package -DskipTests'
                echo 'Maven build Completed'
            }
        }
    stage('JUnit Test') {
            steps {
                // Run unit tests
                script {
                    try {
                        bat 'mvn clean test surefire-report:report' 
                    } catch (err) {
                        currentBuild.result = 'FAILURE'
                        echo 'Unit tests failed!'
                        error 'Unit tests failed!'
                    }
                }
                echo 'JUnit test Completed'
            }
        }

stage('SonarCloud analysis') {
    environment {
        SONAR_ORGANIZATION = 'codingwithkavya'
    }
steps {
        script {
            def scannerHome = tool 'SonarQubeScanner'
            withSonarQubeEnv(credentialsId: 'sonarcloud', installationName: 'SonarCloud') {
                bat "${scannerHome}/bin/sonar-scanner -Dsonar.organization=${env.SONAR_ORGANIZATION} -Dsonar.projectKey=codingwithkavya -Dsonar.sources=src -Dsonar.projectBaseDir=. -Dsonar.analysis.mode=preview -Dsonar.github.repository=https://github.com/CodingWithKavya/employees-cicd.git -Dsonar.pullrequest.key=main"
            }
        }
    }
}

    stage('Copy artifact to EC2') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ansible-server',
                            transfers: [
                                sshTransfer(
                                    cleanRemote: false,
                                    excludes: '',
                                    execCommand: '',
                                    execTimeout: 120000,
                                    flatten: false,
                                    makeEmptyDirs: false,
                                    noDefaultExcludes: false,
                                    patternSeparator: '[, ]+',
                                    remoteDirectory: '//opt//deployment',
                                    remoteDirectorySDF: false,
                                    removePrefix: 'target',
                                    sourceFiles: 'target/*.jar'
                                )
                            ],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: false
                        )
                    ]
                )
            }
        }
        stage('Run Docker Container') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ansible-server',
                            transfers: [
                                sshTransfer(
                                    cleanRemote: false,
                                    excludes: '',
                                    execCommand: '''
                                        cd /opt/deployment/
                                        ansible-playbook start_container.yml
                                    ''',
                                    execTimeout: 120000,
                                    flatten: false,
                                    makeEmptyDirs: false,
                                    noDefaultExcludes: false,
                                    patternSeparator: '[, ]+',
                                    remoteDirectory: '',
                                    remoteDirectorySDF: false,
                                    removePrefix: '',
                                    sourceFiles: ''
                                )
                            ],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: false
                        )
                    ]
                )
                 echo 'Deployment Completed'
            }
        }
    

    }
    post {
        failure {
            // This block will execute if any of the previous stages fail, including unit tests
            echo 'One or more stages have failed!'
            echo 'Pipeline Aborted'
        }
        always {
            echo 'always section'
            // Publish Surefire test results
            junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
        }
    }
}
