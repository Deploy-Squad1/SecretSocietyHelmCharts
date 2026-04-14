pipeline {
    agent any

    parameters {
        choice(name: 'SERVICE_NAME', choices: ['map-service', 'voting-service', 'core-service', 'email-service', 'frontend-service'], description: 'Select the service to deploy')
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Enter sha tag for the image to deploy')
    }

    environment {
        AWS_REGION   = 'eu-north-1'
        SERVICE      = "${params.SERVICE_NAME.replace('-service', '')}"
        CLUSTER_NAME = "secret-society-${env.DEPLOY_ENV}"
        NAMESPACE    = "secret-society-${env.DEPLOY_ENV}"
        RELEASE_NAME = "secret-society"
        CHART_PATH   = "./charts/secret-society"
    }

    stages {
        stage('Prepare K8s') {
            steps {
                sh """
                    aws eks update-kubeconfig --name ${CLUSTER_NAME} --region ${AWS_REGION}
                    kubectl create namespace ${NAMESPACE} --dry-run=client -o yaml | kubectl apply -f -
                """
            }
        }

        stage('Sync Secrets') {
            when { expression { env.SERVICE != 'frontend' } }
            steps {
                withCredentials([file(credentialsId: "${env.SERVICE}-env-file", variable: 'SECRET_FILE')]) {
                    sh """
                        kubectl create secret generic ${env.SERVICE}-secret \
                            --from-env-file=\$SECRET_FILE \
                            --namespace ${NAMESPACE} \
                            --dry-run=client -o yaml | kubectl apply -f -
                    """
                }
            }
        }

        stage('Helm Deploy') {
            steps {
                sh """
                    helm upgrade --install ${RELEASE_NAME} ${CHART_PATH} \
                        --namespace ${NAMESPACE} \
                        -f ${CHART_PATH}/env/values.yaml \
                        -f ${CHART_PATH}/env/values-${DEPLOY_ENV}.yaml \
                        --reuse-values \
                        --set ${env.SERVICE}.deployment.image.tag=${params.IMAGE_TAG} \
                        --wait \
                        --wait-for-jobs \
                        --timeout 5m
                """
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}