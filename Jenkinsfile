pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('checkout') {
            steps {
                checkout scm
            }
        }

        stage('Trivy Scan') {
            agent {
                docker {
                    image 'aquasec/trivy'
                    reuseNode true
                }
            }
            steps {
                script {
                    def imageName = "aquasec/trivy"
                    def imageTag = "latest"  // O el tag específico si lo conoces
                    sh "trivy image --no-progress ${imageName}:${imageTag}"
                }
            }
        }

        stage('Approval for Terraform') {
            steps {
                input(message: 'Approval required before Terraform', ok: 'Proceed', submitterParameter: 'APPROVER')
            }
        }

        stage('terraform') {
            steps {
                sh '/usr/local/bin/terraform apply -auto-approve -no-color'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}

