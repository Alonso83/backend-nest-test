pipeline {

    agent any

    environment {
        NPM_CONFIG_CACHE = "${WORKSPACE}/.npm"
        dockerImage = "us-west1-docker.pkg.dev/lab-agibiz/docker-repository"
        registry = "https://us-west1-docker.pkg.dev"
        registryCredentials = 'gcp-registry-aro'
    }
    stages {
        stage ("saludo al profesor") {
            steps {
                sh 'echo "holaaa profe!!!"'
            }
        }
        stage ("proceso de dependencias, testeo y build") {
            agent {
                docker {
                    image 'node:22'
                    reuseNode true
                }
            }
            stages {
                stage ("instalacion de dependencias") {
                    steps {
                        sh 'npm ci'
                    }
                }
                stage ("ejecucion de test") {
                    steps {
                        sh 'npm run test:cov'
                    }
                }
                stage ("build de la aplicacion") {
                    steps {
                        sh 'npm run build'
                    }
                }
            }
        }
        stage ("build y push img docker") {
            steps {
                script {
                    docker.withRegistry("${registry}", registryCredentials) {
                        sh "docker build -t backend-nest-test-aro ."
                        sh "docker tag backend-nest-test-aro ${dockerImage}/backend-nest-test-aro"
                        sh "docker tag backend-nest-test-aro ${dockerImage}/backend-nest-test-aro:${BUILD_NUMBER}"
                        sh "docker push ${dockerImage}/backend-nest-test-aro"
                        sh "docker push ${dockerImage}/backend-nest-test-aro:${BUILD_NUMBER}"
                    }
                }
            }
        }
        stage ("ejecución de actualización kubernetes") {
            agent {
                docker {
                    image : 'alpine/k8s:1.30.2'
                    reuseNode true
                }
            }
            steps {
                withKubeConfig([credentialsId: 'gcp-kubeconfig']) {
                    sh "kubectl -n lab-aro set image deployments/backend-nest-test-aro backend-nest-test-aro=${dockerImage}/backend-nest-test-aro:${BUILD_NUMBER}"
                }
            }
        }

        stage ("Paso de finalización") {
            steps {
                sh 'echo "proceso finalizado"'
            }
        }
    }
}