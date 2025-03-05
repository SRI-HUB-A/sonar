pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://sonarqube:9000'
        SONARQUBE_SCANNER = 'SonarQubeScanner'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/SRI-HUB-A/sonar.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r app/requirements.txt'
            }
        }

        stage('Run Linting') {
            steps {
                sh 'pylint app/*.py || true'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    sonar-scanner \
                      -Dsonar.projectKey=python_app \
                      -Dsonar.sources=app \
                      -Dsonar.host.url=${SONARQUBE_URL} \
                      -Dsonar.login=${SONAR_AUTH_TOKEN}
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
