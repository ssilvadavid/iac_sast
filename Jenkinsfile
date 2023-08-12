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

        stage('Install tfsec') {
            steps {
                sh "wget -O /var/jenkins_home/tfsec https://github.com/tfsec/tfsec/releases/latest/download/tfsec-linux-amd64"
                sh "chmod +x /var/jenkins_home/tfsec"
            }
        }

        stage('Tfsec Scan') {
            steps {
                dir('iac_sast') {
                    sh "/var/jenkins_home/tfsec"
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



