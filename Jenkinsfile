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
        QA_DH_CREDENTIALS= 'dockerhub-credentials'
        UAT_DH_CREDENTIALS= 'dockerhub-credentials'
        PROD_DH_CREDENTIALS= 'dockerhub-credentials'
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

        stage("promote image DEV to QA"){
            steps{
                script{
                    docker.withRegistry("${env.REGISTRY_URL}","${env.DEV_DH_CREDENTIALS}"){
                        docker.image("${env.BACKEND_IMAGE_DEV}:${GIT_COMMIT}").pull()
                        docker.image("${env.FRONTEND_IMAGE_DEV}:${GIT_COMMIT}").pull()
                    }

                    sh "docker tag ${env.BACKEND_IMAGE_DEV}:${GIT_COMMIT} ${env.BACKEND_IMAGE_QA}:${GIT_COMMIT}"
                    sh "docker tag ${env.FRONTEND_IMAGE_DEV}:${GIT_COMMIT} ${env.FRONTEND_IMAGE_QA}:${GIT_COMMIT}"

                    docker.withRegistry("${env.REGISTRY_URL}","${env.QA_DH_CREDENTIALS}"){
                        docker.image("${env.BACKEND_IMAGE_QA}:${GIT_COMMIT}").push()
                        docker.image("${env.FRONTEND_IMAGE_QA}:${GIT_COMMIT}").push()
                    }
                }
            }
        }

        stage("promote image QA to UAT"){
            steps{
                script{
                    docker.withRegistry("${env.REGISTRY_URL}","${env.QA_DH_CREDENTIALS}"){
                        docker.image("${env.BACKEND_IMAGE_QA}:${GIT_COMMIT}").pull()
                        docker.image("${env.FRONTEND_IMAGE_QA}:${GIT_COMMIT}").pull()
                    }

                    sh "docker tag ${env.BACKEND_IMAGE_QA}:${GIT_COMMIT} ${env.BACKEND_IMAGE_UAT}:${GIT_COMMIT}"
                    sh "docker tag ${env.FRONTEND_IMAGE_QA}:${GIT_COMMIT} ${env.FRONTEND_IMAGE_UAT}:${GIT_COMMIT}"

                    docker.withRegistry("${env.REGISTRY_URL}","${env.UAT_DH_CREDENTIALS}"){
                        docker.image("${env.BACKEND_IMAGE_UAT}:${GIT_COMMIT}").push()
                        docker.image("${env.FRONTEND_IMAGE_UAT}:${GIT_COMMIT}").push()
                    }
                }
            }
        }

        stage("approval for prod"){
            steps{
                input message: 'Promote the image from UAT to PROD?'
                      ok: "Promote to PROD"
            }
        }

        

        stage("Promote image from UAT to PROD"){
            steps{
                script{
                    docker.withRegistry("${env.REGISTRY_URL}","${env.UAT_DH_CREDENTIALS}"){
                        docker.image("${env.BACKEND_IMAGE_UAT}:${GIT_COMMIT}").pull()
                        docker.image("${env.FRONTEND_IMAGE_UAT}:${GIT_COMMIT}").pull()
                    }

                    sh "docker tag ${env.BACKEND_IMAGE_UAT}:${GIT_COMMIT} ${env.BACKEND_IMAGE_PROD}:${GIT_COMMIT}"
                    sh "docker tag ${env.FRONTEND_IMAGE_UAT}:${GIT_COMMIT} ${env.FRONTEND_IMAGE_PROD}:${GIT_COMMIT}"

                    docker.withRegistry("${env.REGISTRY_URL}","${env.PROD_DH_CREDENTIALS}"){
                        docker.image("${env.BACKEND_IMAGE_PROD}:${GIT_COMMIT}").push()
                        docker.image("${env.FRONTEND_IMAGE_PROD}:${GIT_COMMIT}").push()
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
