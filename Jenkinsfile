pipeline {
    agent {
        kubernetes {
            yamlFile 'build-agent.yaml'
        }
    }

    environment {
        IMAGE_NAME = "docker.io/xxxxxx/dso-demo"
        DOCKERFILE_PATH = "${WORKSPACE}/Dockerfile"
        CONTEXT_DIR = "${WORKSPACE}"
    }

    stages {

        stage('Build with Maven') {
            steps {
                container('maven') {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Build Docker Image with Kaniko') {
            steps {
                container('kaniko') {
                    sh """
                    /kaniko/executor \\
                        -f ${DOCKERFILE_PATH} \\
                        -c ${CONTEXT_DIR} \\
                        --insecure \\
                        --skip-tls-verify \\
                        --cache=true \\
                        --destination=${IMAGE_NAME}
                    """
                }
            }
        }

        stage('Post-build cleanup') {
            steps {
                echo "Build complete. Image pushed to ${IMAGE_NAME}"
            }
        }

    }
}
