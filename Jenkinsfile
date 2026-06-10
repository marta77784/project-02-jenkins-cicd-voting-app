pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME = 'marta77784'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Code checked out successfully'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t ${DOCKERHUB_USERNAME}/vote:${IMAGE_TAG} ./vote'
                sh 'docker build -t ${DOCKERHUB_USERNAME}/result:${IMAGE_TAG} ./result'
                sh 'docker build --platform linux/amd64 --build-arg BUILDPLATFORM=linux/amd64 --build-arg TARGETPLATFORM=linux/amd64 --build-arg TARGETARCH=amd64 -t ${DOCKERHUB_USERNAME}/worker:${IMAGE_TAG} ./worker'
            }
        }

        stage('Trivy Security Scan') {
            steps {
                sh 'which trivy || (curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin)'
                sh 'trivy image --exit-code 0 --severity HIGH,CRITICAL --format table ${DOCKERHUB_USERNAME}/vote:${IMAGE_TAG} | tee trivy-vote.txt'
                sh 'trivy image --exit-code 0 --severity HIGH,CRITICAL --format table ${DOCKERHUB_USERNAME}/result:${IMAGE_TAG} | tee trivy-result.txt'
                sh 'trivy image --exit-code 0 --severity HIGH,CRITICAL --format table ${DOCKERHUB_USERNAME}/worker:${IMAGE_TAG} | tee trivy-worker.txt'
                archiveArtifacts artifacts: 'trivy-*.txt', allowEmptyArchive: true
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push ${DOCKERHUB_USERNAME}/vote:${IMAGE_TAG}'
                sh 'docker push ${DOCKERHUB_USERNAME}/result:${IMAGE_TAG}'
                sh 'docker push ${DOCKERHUB_USERNAME}/worker:${IMAGE_TAG}'
            }
        }

        stage('Archive Build Info') {
            steps {
                sh 'echo "Build: ${IMAGE_TAG}" > build-info.txt'
                sh 'echo "vote:${IMAGE_TAG}" >> build-info.txt'
                sh 'echo "result:${IMAGE_TAG}" >> build-info.txt'
                sh 'echo "worker:${IMAGE_TAG}" >> build-info.txt'
                archiveArtifacts artifacts: 'build-info.txt'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
