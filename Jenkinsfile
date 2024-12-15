pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('Docker_Hub_id_pwd') // Docker Hub 자격 증명 (Jenkins에서 설정 필요)
        DOCKER_IMAGE = "cwisky/simple-app:1.0" // Docker 이미지 이름 및 태그
        GITHUB_REPO = "https://github.com/cwisky/simple-app.git" // GitHub 리포지토리 URL
        JAR_FILE = "simple-app.jar" // 다운로드할 JAR 파일 이름
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo "Cloning GitHub repository..."
                    sh "git clone ${GITHUB_REPO} app"
                }
            }
        }
        stage('Prepare JAR File') {
            steps {
                script {
                    echo "Preparing JAR file..."
                    dir('app') {
                        sh "cp target/${JAR_FILE} ../"
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh """
                    cat <<EOF > Dockerfile
                    FROM openjdk:21-jre-slim
                    COPY ${JAR_FILE} /simple-app.jar
                    CMD ["java", "-jar", "/simple-app.jar"]
                    EOF
                    """
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        stage('Tag Docker Image') {
            steps {
                script {
                    echo "Tagging Docker image..."
                    sh "docker tag ${DOCKER_IMAGE} ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    echo "Pushing Docker image to Docker Hub..."
                    sh """
                    echo ${DOCKER_HUB_CREDENTIALS_USR} | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin
                    docker push ${DOCKER_IMAGE}
                    """
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    echo "Running Docker container..."
                    sh "docker run -d -p 80:80 ${DOCKER_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
