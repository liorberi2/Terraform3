pipeline {
    agent any
    tools {
        terraform 'terraform'
    }
    environment {
        AZURE_SUBSCRIPTION_ID='971a8b92-7874-4259-8657-c161685147d0'
        AZURE_TENANT_ID='368897e2-2380-4586-912a-eec098b143fd'
    }

    stages {
        stage('Azure Login') {
            steps {
                   withCredentials([usernamePassword(credentialsId: 'myAzureCredential', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
                            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
                            sh 'az account set -s $AZURE_SUBSCRIPTION_ID'
                        }
            }
        }
        stage('TerraForm initialization') {
          steps {
            sh 'terraform init'
          }
        }
        stage('TerraForm Deploy Infrastructure') {
          steps {
            script {
            def userInput = input(
              id: 'userInput', message: 'Enter username and password for application servers:',
              parameters: [
                string(defaultValue: 'None', description: 'password', name: 'Password'),
                string(defaultValue: 'None', description: 'username', name: 'Username'),
              ])
            // Save variables
            admin_password = userInput.Password?:''
            admin_username = userInput.Username?:''

            //echo to console
            echo ("Your password to Azure servers: ${admin_password}")
            echo ("Your username to Azure Servers: ${admin_username}")
            sh "terraform plan -input=false -var 'admin_password=${admin_password}' -var 'admin_username=${admin_username}'"
            }
          }
        }
    }
}
