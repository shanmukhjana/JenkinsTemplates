pipeline {
    agent any

    environment {
        IMAGE_NAME = 'android-build-env'
    }

    stages {
        stage('Clone Android Project') {
            steps {
                dir('app') {
                    git url: "${SCM_PATH}"
                }
            }
        }

        stage('Clone CI Templates') {
            steps {
                dir('ci-templates') {
                    git url: 'https://github.com/shanmukhjana/JenkinsTemplates.git'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    cp ci-templates/Dockerfile app/
                    cd app
                    docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Build APK in Docker') {
            steps {
                sh '''
                    docker run --rm \
                      -v $PWD/app:/app \
                      -w /app \
                      $IMAGE_NAME ./gradlew assembleDebug
                '''
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'app/app/build/outputs/apk/debug/*.apk', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '✅ Build successful!'
        }
        failure {
            echo '❌ Build failed.'
        }
    }
}
