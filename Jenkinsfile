pipeline {
    agent any

    environment {
        ECR_REGISTRY   = '196253396965.dkr.ecr.ap-south-1.amazonaws.com'
        ECR_REPOSITORY = 'habit-tracker'
        IMAGE_REPO     = "${ECR_REGISTRY}/${ECR_REPOSITORY}"
        AWS_REGION     = 'ap-south-1'
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
                    sh 'mvn sonar:sonar'
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
                        script: 'git rev-parse --short HEAD',
                        returnStdout: true
                    ).trim()

                    echo "Image version: ${env.IMAGE_TAG}"
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t "$IMAGE_REPO:$IMAGE_TAG" .'
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    aws ecr get-login-password --region "$AWS_REGION" |
                    docker login --username AWS --password-stdin "$ECR_REGISTRY"

                    docker push "$IMAGE_REPO:$IMAGE_TAG"
                '''
            }
        }

        stage('Update Helm Configuration') {
            steps {
                sh '''
                    sed -i -E 's/^  tag: .*/  tag: "'$IMAGE_TAG'"/' habit-tracker/values.yaml
                    grep -n "tag:" habit-tracker/values.yaml
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

                        git add habit-tracker/values.yaml

                        git diff --cached --quiet || \
                        git commit -m "Update Habit Tracker image to $IMAGE_TAG"

                        git push origin HEAD:main
                    '''
                }
            }
        }
    }
}

