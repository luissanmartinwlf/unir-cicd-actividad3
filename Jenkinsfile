pipeline {
    agent {
        label 'docker'
    }

    stages {
        stage('Source') {
            steps {
                // Reintenta hasta 3 veces si falla la descarga (fallos de DNS de mi conexion a GitHub)
                retry(3) {
                    git 'https://github.com/srayuso/unir-cicd.git'
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building stage!'
                sh 'make build'
            }
        }

        stage('Unit tests') {
            steps {
                sh 'make test-unit'
                archiveArtifacts artifacts: 'results/*.xml'
            }
        }

        stage('API tests') {
            steps {
                sh 'make test-api'
                archiveArtifacts artifacts: 'results/*.xml'
            }
        }

        stage('E2E tests') {
            steps {
                sh 'make test-e2e'
                archiveArtifacts artifacts: 'results/*.xml'
            }
        }
    }

    post {
        always {
            // Publica los informes unit_result.xml, api_result.xml y cypress_result.xml
            junit allowEmptyResults: true, testResults: 'results/*_result.xml'
        }
        failure {
            echo "Trabajo: ${env.JOB_NAME} - Ejecucion: #${env.BUILD_NUMBER} ha fallado"
            // Linea comentada porque jenkins no tiene configurado el servidor de correo
            // mail to: 'devops@fintech.com',
            //      subject: "Pipeline fallido: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            //      body: "El trabajo ${env.JOB_NAME} ha terminado con fallo en la ejecucion #${env.BUILD_NUMBER}.\nDetalles: ${env.BUILD_URL}"
        }
        cleanup {
            cleanWs()
        }
    }
}
