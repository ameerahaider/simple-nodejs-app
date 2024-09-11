node {
    // Define environment variables
    def GIT_REPO_URL = 'https://github.com/ameerahaider/simple-nodejs-app.git'
    def EC2_SSH_CREDENTIALS_ID = 'ssh-key'
    def EC2_INSTANCE_IP = '52.23.237.13'
    def APP_PATH = '/home/ubuntu'

    // Stage: Clone Repository
    stage('Clone Repository') {
        try {
            git branch: 'main', url: GIT_REPO_URL
        } catch (Exception e) {
            error "Failed to clone repository: ${e.getMessage()}"
        }
    }

    // Stage: Verify SSH Connectivity
    stage('Verify SSH Connectivity') {
        withCredentials([sshUserPrivateKey(credentialsId: EC2_SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
            sh """
                echo "Verifying SSH connection to EC2 instance..."
                ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@${EC2_INSTANCE_IP} 'echo "SSH connection successful!"'
            """
        }
    }

    // Stage: Create App Directory
    stage('Create App Directory') {
        withCredentials([sshUserPrivateKey(credentialsId: EC2_SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
            sh """
                echo "Making Directory for APP..."
                ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@${EC2_INSTANCE_IP} "mkdir -p ${APP_PATH}"
            """
        }
    }

    // Stage: Copy Files to EC2
    stage('Copy Files to EC2') {
        withCredentials([sshUserPrivateKey(credentialsId: EC2_SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
            sh """
                echo "Copying files to EC2 instance..."
                echo "Files in workspace:"
                ls -l

                scp -o StrictHostKeyChecking=no -i $SSH_KEY -r * ubuntu@${EC2_INSTANCE_IP}:${APP_PATH}
            """
        }
    }

    // Stage: Run Deployment Commands
    stage('Run Deployment Commands') {
        withCredentials([sshUserPrivateKey(credentialsId: EC2_SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
            sh """
                echo "Running commands on EC2 instance..."
                ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@${EC2_INSTANCE_IP} "
                    cd ${APP_PATH}
                    npm install
                    nohup npm start &> app.log &
                "
            """
        }
    }

    // Always clean workspace
    cleanWs()
}