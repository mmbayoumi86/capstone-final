pipeline {
    environment {
        eksClusterName = 'my-cluster'
        eksRegion = 'us-west-2'
        dockerHub = 'mmbayoumi86'
        dockerImage = 'capstone-final'
        dockerVersion = '0.3'
    }
    agent any
    stages {
        stage('Lint') {
            steps {
                sh 'tidy -q -e **/*.html'
                sh '''docker run --rm -i hadolint/hadolint < Dockerfile'''
            }
        }
        stage('Docker build') {
            steps {
                script {
                    dockerImage = docker.build('${dockerHub}/${dockerImage}:${dockerVersion}')
                    docker.withRegistry('', 'docker-hub-creds') {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('K8S Deploy')  {
            steps {
                withAWS(credentials: 'aws-creds', region: eksRegion) {
                    sh 'aws eks --region=${eksRegion} update-kubeconfig --name ${eksClusterName}'
                    sh 'kubectl apply -f k8s/capstone-deployment.yml'
                }
            }
        }
    }
}