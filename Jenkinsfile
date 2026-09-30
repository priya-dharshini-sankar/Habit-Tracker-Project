pipeline {
    agent any

    environment {
        AWS_REGION     = 'ap-south-1'
        ECR_REGISTRY   = '196253396965.dkr.ecr.ap-south-1.amazonaws.com'
        ECR_REPOSITORY = 'habit-tracker'
        IMAGE_REPO     = "${ECR_REGISTRY}/${ECR_REPOSITORY}"
        HELM_VALUES    = 'habit-tracker/values.yaml'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=habit-tracker \
                        -Dsonar.projectName="Habit Tracker"
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Image Versioning') {
            steps {
                script {
                    env.IMAGE_TAG = sh(
                        script: 'git rev-parse --short=7 HEAD',
                        returnStdout: true
                    ).trim()

                    echo "Docker Image Tag: ${env.IMAGE_TAG}"
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                    -t "${IMAGE_REPO}:${IMAGE_TAG}" .
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    aws ecr get-login-password --region "${AWS_REGION}" |
                    docker login --username AWS --password-stdin "${ECR_REGISTRY}"

                    docker push "${IMAGE_REPO}:${IMAGE_TAG}"
                '''
            }
        }

        stage('Update Helm Configuration') {
            steps {
                sh '''
                    sed -i -E "s/^([[:space:]]*tag:).*/\\1 \\"${IMAGE_TAG}\\"/" "${HELM_VALUES}"

                    echo "Updated Helm image tag:"
                    grep -n "tag:" "${HELM_VALUES}"
                '''
            }
        }

        stage('Commit Deployment Change') {
            steps {
                withCredentials([
                    gitUsernamePassword(
                        credentialsId: 'github-push',
                        gitToolName: 'Default'
                    )
                ]) {
                    sh '''
                        git config user.name "Jenkins"
                        git config user.email "jenkins@localhost"

                        git add "${HELM_VALUES}"

                        if ! git diff --cached --quiet; then
                            git commit -m "Update Habit Tracker image to ${IMAGE_TAG}"
                        else
                            echo "No Helm configuration changes to commit."
                        fi

                        git push origin HEAD:main
                    '''
                }
            }
        }
    }
}