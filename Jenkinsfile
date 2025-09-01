pipeline {
    agent any
    tools { nodejs 'nodejs-Its' }

    environment {
        SONARQUBE = credentials('SonarQube') // ชื่อ Credential ของ Jenkins
    }

    parameters {
    string(name: 'GIT_BRANCH', defaultValue: 'main', description: 'Branch to build')
  }

    stages {
        stage('Checkout') {
            steps {
                git branch: params.GIT_BRANCH, url: 'https://github.com/Navaphon001/Learn-SonarQube'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Show code') {
        steps {
            sh '''
                echo "---- utils.js ----"; sed -n '1,120p' utils.js || true
                echo "---- server.js ----"; sed -n '1,120p' server.js || true
            '''
        }
}

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh """
                        npx sonar-scanner \
                            -Dsonar.projectKey=Simple-Jenkins \
                            -Dsonar.host.url=$SONAR_HOST_URL \
                            -Dsonar.token=$SONAR_AUTH_TOKEN
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
