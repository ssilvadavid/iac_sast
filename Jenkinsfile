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

        stage('Download Terrascan') {
            steps {
                sh "curl -LO https://github.com/accurics/terrascan/releases/latest/download/terrascan-linux-amd64"
                sh "chmod +x terrascan-linux-amd64"
                sh "mv terrascan-linux-amd64 /usr/local/bin/terrascan"
            }
        }

        stage('Terrascan Scan') {
            steps {
                dir('iac_sast') {
                    sh "terrascan scan -f ."
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


