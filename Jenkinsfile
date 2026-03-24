pipeline {
    agent any

    parameters{
        choice(name: 'SERVICE_NAME', choices: ['map-service', 'voting-service', 'core-service', 'email-service', 'frontend-service'], description: 'Select the service to deploy')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'stage', 'prod'], description: 'Select the environment to deploy to')
        string(name: 'IMAGE_TAG', description: 'Enter sha tag for the image to deploy')
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
                        env.CLUSTER_NAME = "secret-society-dev"
                    } else if (params.ENVIRONMENT == 'stage') {
                        env.TARGET_ROLE = "arn:aws:iam::${STAGE_ACCOUNT_ID}:role/TerraformDeployRole"
                        env.CLUSTER_NAME = "secret-society-stage"
                    } else if (params.ENVIRONMENT == 'prod') {
                        env.TARGET_ROLE = "arn:aws:iam::${PROD_ACCOUNT_ID}:role/TerraformDeployRole"
                        env.CLUSTER_NAME = "secret-society-prod"
                    }
                    env.NAMESPACE = "secret-society-${params.ENVIRONMENT}"
                    env.RELEASE_NAME = "secret-society"
                    env.CHART_PATH = "./charts/secret-society"
                }
                withAWS(role: "${env.TARGET_ROLE}", region: "${AWS_REGION}") {
                    sh """
                        aws eks update-kubeconfig --name ${env.CLUSTER_NAME} --region ${AWS_REGION}

                        helm upgrade --install ${env.RELEASE_NAME} ${env.CHART_PATH} \
                            --namespace ${env.NAMESPACE} \
                            --create-namespace \
                            --set ${params.SERVICE_NAME}.deployment.image.repository=${ECR_REPO} \
                            --set ${params.SERVICE_NAME}.deployment.image.tag=${params.IMAGE_TAG} \
                            --reuse-values \
                            --wait --timeout 5m
                    """
                }
            }
        }
    }
}