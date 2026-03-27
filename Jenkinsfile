pipeline {
    agent {
        kubernetes {
            yamlFile 'build-agent.yaml'  // your pod template file
        }
    }

    environment {
        NVD_API_KEY = credentials('nvd-api-key') // store your NVD API key in Jenkins credentials
    }

    stages {
        stage('Build') {
            steps {
                container('maven') {
                    sh 'mvn clean install -B'
                }
            }
        }

        stage('Dependency Check') {
            steps {
                container('maven') {
                    sh '''
                        mvn org.owasp:dependency-check-maven:6.1.1:check \
                            -Dnvd.apiKey=$NVD_API_KEY
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                container('kaniko') {
                    sh '''
                        /kaniko/executor \
                        -f /home/jenkins/agent/workspace/${JOB_NAME}/Dockerfile \
                        -c /home/jenkins/agent/workspace/${JOB_NAME} \
                        --destination=docker.io/xxxxxx/dso-demo \
                        --cache=true
                    '''
                }
            }
        }
    }
}
