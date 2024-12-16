pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "cwisky/simple-app:1.0"
        GITHUB_REPO = "https://github.com/cwisky/simple-app.git"
        JAR_FILE = "simple-app.jar"
    }

    stages {
        
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
                echo 'Cleaned up' >> app.log
            }
        }

        stage('Git Clone') {
            steps {
                echo 'Cloning Git repository...'
                git 'https://github.com/cwisky/simple.app'
            }
        }
        /*
        stage('Clone Repository') {
            steps {
                script {
                    echo "Cloning GitHub repository..." >> app.log
                    withCredentials([usernamePassword(credentialsId: 'GitHub_ID_PWD', usernameVariable: 'GITHUB_USER', passwordVariable: 'GITHUB_TOKEN')]) {
                        sh "git clone https://${GITHUB_USER}:${GITHUB_TOKEN}@github.com/cwisky/simple-app.git app"
                    }
                }
            }
        }

        stage('Prepare JAR File') {
            steps {
                script {
                    echo "Preparing JAR file..." >> app.log
                    dir('app') {
                        sh """
                        if [ -f ${JAR_FILE} ]; then
                            echo "JAR file ${JAR_FILE} found." >> ../app.log
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
                    echo "Building Docker image..." >> app.log
                    writeFile file: 'Dockerfile', text: """
                    FROM openjdk:21-jre-slim
                    COPY ${JAR_FILE} /simple-app.jar
                    CMD ["java", "-jar", "/simple-app.jar"]
                    """
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'Docker_Hub_id_pwd', usernameVariable: 'DOCKER_HUB_CREDENTIALS_USR', passwordVariable: 'DOCKER_HUB_CREDENTIALS_PSW')]) {
                        echo "Pushing Docker image to Docker Hub..."
                        sh """
                        echo ${DOCKER_HUB_CREDENTIALS_PSW} | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin
                        docker push ${DOCKER_IMAGE}
                        """
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    echo "Running Docker container..." >> app.log
                    sh """
                    docker ps -q --filter 'ancestor=${DOCKER_IMAGE}' | xargs --no-run-if-empty docker stop
                    docker run -d -p 8080:80 ${DOCKER_IMAGE}
                    """
                }
            }
        }*/
    }
    /*
    post {
        always {
            echo "Cleaning up workspace..." >> app.log
            //cleanWs()
        }
    }*/
}
