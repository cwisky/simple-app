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
                //cleanWs()
                echo 'Cleaned up'
            }
        }

        stage('Git Clone') {
            steps {
                echo 'Cloning Git repository...'
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[
                        credentialsId: 'Github_login',       // Jenkins에서 github의 id, pwd를 등록하고 아이디를 'GitHub_ID_PWD' 으로 지정해야 함
                        url: GITHUB_REPO
                    ]]
                ])
                // JAR 파일에 실행 권한 추가
                sh 'chmod +x ./${JAR_FILE}'
            }
        }
        
        stage('Prepare JAR File') {
            steps {
                script {
                    echo "Preparing JAR file..."
                    //dir('app') {
                        sh """
                        if [ -f ${JAR_FILE} ]; then
                            echo "JAR file ${JAR_FILE} found."
                        else
                            echo "JAR file not found. Ensure the project is built correctly."
                            exit 1
                        fi
                        """
                   // }
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..." 
                    writeFile file: 'Dockerfile', text: """
                    FROM openjdk:21-slim
                    COPY ${JAR_FILE} /simple-app.jar
                    CMD ["java", "-jar", "/simple-app.jar"]
                    """
                    sh "docker build -t ${DOCKER_IMAGE} ."
                    echo "Docker image created!" 
                }
            }
        }

        stage('Docker Login') {  // Docker Hub에 도커 이미지를 업로드할 때 요구됨
            steps {
                script {
                    // Docker Hub 로그인 (Jenkins에 등록된 Docker Hub Credentials 사용)
                    echo "Logging in to Docker Hub..."
                    withCredentials([usernamePassword(
                        credentialsId: 'Docker_Hub_Credentials', //Jenkins 관리 > Credentials에 등록된 Credentials ID( Jenkins 관리 > Credentials 에서 확인 가능)
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD')]) {
                        
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USER} --password-stdin"
                    }
                    echo "Login to Docker Hub Success!"
                }
            }
        }
        
        stage('Docker Push') {
            steps {
                script {
                    // Docker Hub에 이미지 푸시
                    echo "Pushing Docker Image to Docker Hub..."
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    echo "Running Docker container..."
                    sh """
                    docker ps -q --filter 'ancestor=${DOCKER_IMAGE}' | xargs --no-run-if-empty docker stop
                    docker run -d ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }
    /*
    post {
        success {
            echo "Docker image has been successfully uploaded to Docker Hub."
        }
        failure {
            echo "Pipeline failed. Check the logs for details."
        }
    }*/
}
