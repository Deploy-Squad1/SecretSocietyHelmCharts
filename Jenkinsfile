pipeline {
    agent any

    parameters{
        choice(name: 'SERVICE_NAME', choices: ['map-service', 'voting-service', 'core-service', 'email-service', 'frontend-service'], description: 'Select the service to deploy')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'stage', 'prod'], description: 'Select the environment to deploy to')
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Enter the image tag to deploy')
    }

    environment{
        AWS_REGION = 'eu-north-1'
        DEV_ACCOUNT_ID   = credentials('aws-dev-account-id')
        STAGE_ACCOUNT_ID = credentials('aws-stage-account-id')
        PROD_ACCOUNT_ID  = credentials('aws-prod-account-id')

        ECR_REGISTRY = "${DEV_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        ECR_REPO = "${ECR_REGISTRY}/${params.SERVICE_NAME}"
    }

    stages {
        stage('Deploy to EKS') {
            steps {
                script {
                    if (params.ENVIRONMENT == 'dev'){
                        env.TARGET_ROLE = ""
                        env.CLUSTER_NAME = "dev-cluster"
                    } else if (params.ENVIRONMENT == 'stage') {
                        env.TARGET_ROLE = "arn:aws:iam::${STAGE_ACCOUNT_ID}:role/TerraformDeployRole"
                        env.CLUSTER_NAME = "stage-cluster"
                    } else if (params.ENVIRONMENT == 'prod') {
                        env.TARGET_ROLE = "arn:aws:iam::${PROD_ACCOUNT_ID}:role/TerraformDeployRole"
                        env.CLUSTER_NAME = "prod-cluster"
                    }
                    env.NAMESPACE = "secret-society-${params.ENVIRONMENT}"
                }
                withAWS(role: "${env.TARGET_ROLE}", region: "${AWS_REGION}") {
                    sh """
                        aws eks update-kubeconfig --name ${env.CLUSTER_NAME} --region ${AWS_REGION}

                        helm upgrade --install ${params.SERVICE_NAME} ./${params.SERVICE_NAME} \
                            --namespace ${env.NAMESPACE} \
                            --create-namespace \
                            --set image.repository=${ECR_REPO} \
                            --set image.tag=${params.IMAGE_TAG} \
                            --wait --timeout 5m
                    """
                }
            }
        }
    }
}