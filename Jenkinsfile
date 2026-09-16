pipeline {
    agent { label 'docker' }

    options {
        skipDefaultCheckout()
        disableConcurrentBuilds()
        timestamps()
    }

    environment {
        IMAGE_NAME = 'devops-capstone'
        CONTAINER_NAME = 'devops-capstone-web'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t "$IMAGE_NAME:jenkins-$BUILD_NUMBER" .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    set -eu

                    ./deploy.sh

                    if docker container inspect "$CONTAINER_NAME" >/dev/null 2>&1; then
                        docker stop "$CONTAINER_NAME"
                        docker rm "$CONTAINER_NAME"
                    fi

                    docker run -d \
                        --name "$CONTAINER_NAME" \
                        -p 8080:80 \
                        "$IMAGE_NAME:jenkins-$BUILD_NUMBER"

                    ready=0

                    for attempt in 1 2 3 4 5; do
                        if curl --fail --silent --show-error \
                            http://localhost:8080/ >/dev/null; then
                            ready=1
                            break
                        fi
                        sleep 2
                    done

                    if [ "$ready" -ne 1 ]; then
                        docker logs "$CONTAINER_NAME"
                        exit 1
                    fi

                    echo "Website verified successfully"
                '''
            }
        }
    }

    post {
        success {
            echo 'Checkout, Build, and Deploy completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check the failed stage logs.'
        }
    }
}
