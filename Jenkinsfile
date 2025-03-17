pipeline {
    agent any

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
        choice(name: 'action', choices: ['apply', 'destroy'], description: 'Select the action to perform')
    }

    environment {
        ARM_CLIENT_ID       = '1955edbb-79f8-4cc0-8ddb-2a465ff3e70d'  // Your App ID (Client ID)
        ARM_CLIENT_SECRET   = 'cjC8Q~vE0B_Yo4kQrQJQuwCH__bI5zdem8Wy0c6V'  // Your Secret (Client Secret)
        ARM_SUBSCRIPTION_ID = '79a60dcf-4a07-480c-b5d2-77180e854c82'  // Your Subscription ID
        ARM_TENANT_ID       = '0bca7ac3-fcb6-4efd-89eb-6de97603cf21'  // Your Tenant ID
    }
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/raghuvamsi119/terraform-jenkins-pipeline.git'
            }
        }
        stage('Terraform init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Plan') {
            steps {
                sh 'terraform plan -out tfplan'
                sh 'terraform show -no-color tfplan > tfplan.txt'
            }
        }
        stage('Apply / Destroy') {
            steps {
                script {
                    if (params.action == 'apply') {
                        if (!params.autoApprove) {
                            def plan = readFile 'tfplan.txt'
                            input message: "Do you want to apply the plan?",
                            parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                        }

                        sh 'terraform ${action} -input=false tfplan'
                    } else if (params.action == 'destroy') {
                        sh 'terraform ${action} --auto-approve'
                    } else {
                        error "Invalid action selected. Please choose either 'apply' or 'destroy'."
                    }
                }
            }
        }

    }
}
