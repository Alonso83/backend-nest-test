pipeline {

    agent any

    environment {
        NPM_CONFIG_CACHE = "${WORKSPACE}/.npm"
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
    }
}