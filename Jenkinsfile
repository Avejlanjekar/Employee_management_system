pipeline{
    agent any
    environment{
        FRONTEND_IMAGE= 'avejlanjekar45/employee-management-frontend-deployment'
        BACKEND_IMAGE= 'avejlanjekar45/employee-management-backend-deployment'

        REGISTRY_URL= 'https://registry.hub.docker.com'
        CREDENTIALS= 'dockerhub-credentials'
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
                    def backend= docker.build("${env.BACKEND_IMAGE}:${GIT_COMMIT}","./backend")

                    def frontend= docker.build("${env.FRONTEND_IMAGE}:${GIT_COMMIT}","./frontend")

                    docker.withRegistry("${env.REGISTRY_URL}","${env.CREDENTIALS}"){
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
