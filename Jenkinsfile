pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'sarweshvaran/ci-cd-flask-app'
        SONARQUBE_ENV = 'sonar_server' // Must match Jenkins SonarQube server name
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                bat 'pip install -r requirements.txt'
                bat 'pytest test_app.py'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    bat '"%SONAR_SCANNER_HOME%\\bin\\sonar-scanner.bat"'
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

        stage('Docker Build') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Dockerhub-key', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat 'echo %PASS% | docker login -u %USER% --password-stdin'
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                bat "docker run -d -p 5000:5000 ${DOCKER_IMAGE}"
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs for details.'
        }
    }
}
