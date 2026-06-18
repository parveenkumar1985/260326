pipeline {
    agent any

    environment {
        DOCKER_REPO = "parveenkumar1985/17march2026"
    }

    stages {

        stage('Display Build Information') {
            steps {
                sh '''
                    echo "We are starting build and push Docker image"
                    echo "Project (JOBName) & Build Number ---> ${JOB_NAME}-${BUILD_NUMBER}"
                    ls -ltr
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "==========Now Build Starting==========="
                    docker build -t ${DOCKER_REPO}:${JOB_NAME}-${BUILD_NUMBER} --no-cache .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    docker image push ${DOCKER_REPO}:${JOB_NAME}-${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl get all

                    kubectl delete svc mytg-svc -n mytg || echo "No such service found, continue...."

                    sed -i "s/JOB_NAME/${JOB_NAME}/g" tomcat-deploy.yaml
                    sed -i "s/BUILD_NUMBER/${BUILD_NUMBER}/g" tomcat-deploy.yaml

                    kubectl apply -f mynps.yaml
                    kubectl apply -f tomcat-deploy.yaml

                    kubectl get all -n mytg
                    kubectl get svc -n mytg
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}