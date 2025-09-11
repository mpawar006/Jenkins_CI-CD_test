pipeline {
    agent any

    stages {
        stage('git') {
            agent { label 'k8-m' }
            steps {
                git branch: 'master', url: 'https://github.com/mpawar006/Jenkins_CI-CD_test.git'
            }
        }

        stage('docker') {
            agent { label 'k8-m' }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                                 usernameVariable: 'DOCKER_USER',
                                                 passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        docker build . -t mpawar006/testproject
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push mpawar006/testproject
                    '''
                }
            }
        }

        stage('k8') {
            agent { label 'k8-m' }
            steps {
                sh '''
                if kubectl get deploy website > /dev/null 2>&1; then
                  echo "Existing deployment found. Deleting..."
                  kubectl delete deploy website
                else
                  echo "No existing deployment found. Skipping delete."
                fi
                '''
                sh 'kubectl apply -f deploy.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
