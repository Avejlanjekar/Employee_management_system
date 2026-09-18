pipeline{
    agent any
    environment{
        FRONTEND_IMAGE_DEV= 'avejlanjekar45/employee-management-frontend-dev'
        FRONTEND_IMAGE_QA= 'avejlanjekar45/employee-management-frontend-qa'
        FRONTEND_IMAGE_UAT= 'avejlanjekar45/employee-management-frontend-uat'
        FRONTEND_IMAGE_PROD='avejlanjekar45/employee-management-frontend-prod'

        BACKEND_IMAGE_DEV= 'avejlanjekar45/employee-management-backend-dev'
        BACKEND_IMAGE_QA= 'avejlanjekar45/employee-management-backend-qa'
        BACKEND_IMAGE_UAT='avejlanjekar45/employee-management-backend-uat'
        BACKEND_IMAGE_PROD='avejlanjekar45/employee-management-backend-prod'

        REGISTRY_URL= 'https://registry.hub.docker.com'

        DEV_DH_CREDENTIALS= 'dockerhub-credentials'
    }

    stages{

        stage('checkout'){
            steps{
                checkout scm
            }
        }

        stage("build & push"){
            steps{
                script{
                    def backend= docker.build("${env.BACKEND_IMAGE_DEV}:${GIT_COMMIT}","./backend")

                    def frontend= docker.build("${env.FRONTEND_IMAGE_DEV}:${GIT_COMMIT}","./frontend")

                    docker.withRegistry("${env.REGISTRY_URL}","${env.DEV_DH_CREDENTIALS}"){
                        backend.push()
                        frontend.push()
                    }
                }
            }
        }
    }
    post{
        always{
            cleanWs()
        }
    }
}
