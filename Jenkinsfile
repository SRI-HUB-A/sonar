pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://sonarqube:9000' // SonarQube URL
        SONARQUBE_SCANNER = 'SonarQubeScanner'   // Sonar Scanner Tool Name
    }

    stages {
        stage('Checkout Code') {
            steps {
                sh 'git clone https://github.com/SRI-HUB-A/sonar'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                cd sonar
                pip install -r app/requirements.txt
                '''
            }
        }

        stage('Run Linting') {
            steps {
                sh '''
                cd sonar
                pylint app/*.py || true
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { // SonarQube tool name from Jenkins
                    sh '''
                    cd sonar
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

    post {
        success {
            echo '✅ Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
