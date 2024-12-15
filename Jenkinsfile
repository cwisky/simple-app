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
                    echo "Cloning GitHub repository..." >> app.log
                    sh "git clone ${GITHUB_REPO} app || echo 'Git clone failed. Check repository URL or credentials.' >> app.log"
                }
            }
        }

        stage('Set Permissions') {
            steps {
                echo 'Setting permissions for Jenkins-auto-cicd-test directory...'
                sh 'chmod -R 755 /var/lib/jenkins/workspace/Jenkins-auto-cicd-test/'
            }
        }
        stage('Prepare JAR File') {
            steps {
                script {
                    echo "Preparing JAR file..." >> app.log
                    dir('app') {
                        sh """
                        if [ -f ${JAR_FILE} ]; then
                            //cp target/${JAR_FILE} ../
                        else
                            echo "JAR file not found. Ensure the project is built correctly." >> ../app.log
                            exit 1
                        fi
                        """
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."  >> app.log
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
                    echo "Tagging Docker image..." >> app.log
                    sh "docker tag ${DOCKER_IMAGE} ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    echo "Pushing Docker image to Docker Hub..." >> app.log
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
                    echo "Running Docker container..." >> app.log
                    sh "docker run -d -p 80:80 ${DOCKER_IMAGE}"
                }
            }
        }
    }
/*
    post {
        always {
            echo "Cleaning up workspace..." >> app.log
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully!" >> app.log
        }
        failure {
            echo "Pipeline failed. Check logs for details." >> app.log
        }
    }*/
}
