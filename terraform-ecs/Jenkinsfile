pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('awscli')
        AWS_SECRET_ACCESS_KEY = credentials('awscli')
    }

    stages {
        stage('Checkout') {
            steps {
                 dir (/root/){
                git url: 'https://github.com/toufikj/terra.git', branch: 'master'
                 }
            }
        }

        stage('Terraform Init') {
            steps {
                dir('terraform-ecs') {
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir('terraform-ecs') {
                    sh 'terraform plan -out=tfplan'
                }
            }
        }

        stage('Approval') {
            when {
                not {
                    equals expected: true, actual: params.autoApprove
                }
            }
            steps {
                script {
                    def plan = readFile 'terraform-ecs/tfplan'
                    input message: "Do you want to apply this plan?",
                          parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform-ecs') {
                    sh 'terraform apply -input=false tfplan'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

