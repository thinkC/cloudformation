// pipeline {
//     agent any
//         environment {
//         AWS_DEFAULT_REGION = 'us-east-1' // Specify your AWS region here
//         AWS_ACCESS_KEY_ID = 'AKIAWQRWYLPHFM4N42N7'
//         AWS_SECRET_ACCESS_KEY = 'ugxWFhHWmfUnq8kCLKQDFFbswdd/BRIaEnq9RnCD'
//     }
//     stages {
//         stage('Verify AWS Credentials') {
//             steps {
//                 script {
//                     sh "aws sts get-caller-identity"
//                 }
//             }
//         }
        
//         stage('Deploy EC2 Stack') {
//             steps {
//                 script {
//                     def amiId = 'ami-053b0d53c279acc90'
//                     def securityGroups = 'frontendsg'
//                     def keyPairName = 'aws_sre'
//                     def instanceType = 't2.micro'
//                     def templatePath = "${WORKSPACE}/1.template1.yml"
                    
//                     sh "aws cloudformation create-stack --stack-name MyEC2Stack --template-body file://${templatePath} --parameters ParameterKey=AMIId,ParameterValue=${amiId} ParameterKey=SecurityGroups,ParameterValue=${securityGroups} ParameterKey=KeyPairName,ParameterValue=${keyPairName} ParameterKey=InstanceType,ParameterValue=${instanceType} --region us-east-1"
//                 }
//             }
//         }
//     }
// }



pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        SECRET_NAME = 'create-ec2' // Replace with your secret name
    }

    stages {
stages {
    stage('Install jq') {
        steps {
            sh 'sudo apt-get install -y jq' // Adjust the package manager command if needed
        }
    }

        stage('Deploy EC2 Stack') {
            steps {
                script {
                    // Retrieve the secret value from AWS Secrets Manager
                    def secretValue = sh(script: "aws secretsmanager get-secret-value --secret-id ${SECRET_NAME} --region ${AWS_DEFAULT_REGION} | jq -r .SecretString", returnStdout: true).trim()

                    // Parse the secret value as JSON (assuming it's in JSON format)
                    def secretJson = new groovy.json.JsonSlurper().parseText(secretValue)

                    // Set environment variables for AWS credentials
                    withCredentials([
                        string(credentialsId: secretJson.accessKeyId, variable: 'AWS_ACCESS_KEY_ID'),
                        string(credentialsId: secretJson.secretAccessKey, variable: 'AWS_SECRET_ACCESS_KEY')
                    ]) {
                        sh "aws cloudformation create-stack --stack-name MyEC2Stack --template-body file://${templatePath} --parameters ParameterKey=AMIId,ParameterValue=${amiId} ParameterKey=SecurityGroups,ParameterValue=${securityGroups} ParameterKey=KeyPairName,ParameterValue=${keyPairName} ParameterKey=InstanceType,ParameterValue=${instanceType}"
                    }
                }
            }
        }
    }
}
}