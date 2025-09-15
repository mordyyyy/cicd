pipeline {
    agent { label 'docker'}

    environment {
        DOCKER_CREDS = credentials('dockerhub-PAT') // Jenkins credentials ID for Docker Hub
        ENV_FILE = '.env.prod' // You can change to .env.local for local deploy
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo '📦 Cloning repository...'
                git branch: 'main', url: 'https://github.com/devopacademyv1-a11y/docker-compose-jenkins-lab.git', credentialsId: '18bbb0eb-e854-4a00-8846-43026a446f91'
            }
        }

        stage('Docker Login') {
            steps {
                echo '🔐 Logging in to Docker Hub...'
                sh """
                    echo "${DOCKER_CREDS_PSW}" | docker login -u "${DOCKER_CREDS_USR}" --password-stdin
                """
            }
        }

        stage('Build & Push Server Image') {
            steps {
                echo '⚙️ Building server image...'
                sh """
                    docker build -t ${DOCKER_CREDS_USR}/backend:latest ./server
                    docker push ${DOCKER_CREDS_USR}/backend:latest
                """
            }
        }

        stage('Build & Push Client Image') {
            steps {
                echo '⚙️ Building client image...'
                sh """
                    docker build -t ${DOCKER_CREDS_USR}/frontend:latest ./client
                    docker push ${DOCKER_CREDS_USR}/frontend:latest
                """
            }
        }

        stage('Stop & Remove Old Containers') {
            steps {
                echo '🛑 Stopping and removing old containers...'
                sh """
                    docker-compose --env-file ${ENV_FILE} down -v --remove-orphans || true
                    docker ps -aq | xargs -r docker rm -f || true
                """
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                echo '🚀 Deploying new containers...'
                sh """
                    docker-compose --env-file ${ENV_FILE} pull
                    docker-compose --env-file ${ENV_FILE} up -d
                """
            }
        }
    }

    post {
        success {
            echo "✅ Build, push, and deployment completed successfully."
        }
        failure {
            echo "❌ Build, push, or deployment failed."
        }
    }
}
