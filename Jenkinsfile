pipeline {
    agent any

    tools {
        nodejs 'NodeJS' // Ensure this name matches the one configured in Jenkins global tools
    }

    environment {
        DOCKER_CREDS = credentials('dockerhub-credentials')
        APP_DIR = 'application'
        APP_NAME = 'employee-management'
        NAMESPACE = 'default'
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
        KUBECTL = "/usr/local/bin/kubectl"
        HELM = "/usr/local/bin/helm"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
                sh 'echo "Code checked out successfully"'
                sh 'ls -la'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir(APP_DIR) {
                    sh 'node -v'
                    sh 'npm -v'
                    sh 'npm install --save-dev @babel/plugin-proposal-private-property-in-object'
                    sh 'npm ci'
                }
            }
        }

        stage('Build Application') {
            steps {
                dir(APP_DIR) {
                    sh 'CI=true npm run build'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir(APP_DIR) {
                    sh 'CI=true npm test -- --watchAll=false --passWithNoTests'
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    def commitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    def dockerImage = "${DOCKER_CREDS_USR}/${APP_NAME}"

                    dir(APP_DIR) {
                        sh "echo ${DOCKER_CREDS_PSW} | docker login -u ${DOCKER_CREDS_USR} --password-stdin"
                        sh "docker build -t ${dockerImage}:${commitHash} -t ${dockerImage}:latest ."
                        sh "docker push ${dockerImage}:${commitHash}"
                        sh "docker push ${dockerImage}:latest"
                    }

                    env.IMAGE_NAME = dockerImage
                    env.IMAGE_TAG = commitHash
                }
            }
        }

        stage('Update Helm Chart') {
            steps {
                dir('helm/employee-management') {
                    sh """
                    sed -i 's|repository:.*|repository: ${env.IMAGE_NAME}|g' values.yaml
                    sed -i 's|tag:.*|tag: "${env.IMAGE_TAG}"|g' values.yaml
                    cat values.yaml
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                dir('helm/employee-management') {
                    sh "${KUBECTL} config current-context"
                    sh "${KUBECTL} get nodes"

                    sh "${HELM} upgrade --install ${APP_NAME} . --namespace ${NAMESPACE} --create-namespace --atomic --timeout 5m0s"
                    sh "${KUBECTL} rollout status deployment/${APP_NAME} -n ${NAMESPACE} --timeout=180s"
                    sh "${KUBECTL} get services -n ${NAMESPACE} | grep ${APP_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline succeeded! Application is deployed.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
        always {
            script {
                try {
                    if (env.IMAGE_TAG) {
                        sh "docker rmi ${env.IMAGE_NAME}:${env.IMAGE_TAG} || true"
                        sh "docker rmi ${env.IMAGE_NAME}:latest || true"
                    }
                    sh "docker system prune -f || true"
                } catch (Exception e) {
                    echo "⚠️ Docker cleanup failed: ${e.message}"
                }
            }
        }
    }
}
