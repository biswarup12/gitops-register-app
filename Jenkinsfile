pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        APP_NAME = "register-app-pipeline"
    }

    parameters {
        string(
            name: 'IMAGE_TAG',
            defaultValue: '',
            description: 'Docker image tag received from Pipeline A'
        )
    }

    stages {

        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main',
                    credentialsId: 'github',
                    url: 'https://github.com/biswarup12/gitops-register-app'
            }
        }

        stage("Update the Deployment Tag") {
            steps {
                sh '''
                    echo "IMAGE_TAG received from Pipeline A: ${IMAGE_TAG}"

                    echo "Before update:"
                    cat deployment.yaml

                    sed -i -E "s|(image: biswarup1706/register-app-pipeline:).*|\\1${IMAGE_TAG}|" deployment.yaml

                    echo "After update:"
                    cat deployment.yaml
                '''
            }
        }

        stage("Push the Changed Deployment File to Git") {
            steps {
                script {
                    sh """
                        git config user.name "biswarup12"
                        git config user.email "biswarupmondal2012@gmail.com"
                        git add deployment.yaml
                        git commit -m "Update register-app image to ${IMAGE_TAG}" || echo "No changes to commit"
                    """
                    
                    withCredentials([
                        gitUsernamePassword(
                            credentialsId: 'github',
                            gitToolName: 'Default'
                        )
                    ]) {
                        // Changing to triple double quotes allows Jenkins credential injection to work
                        sh """
                            git push https://github.com main
                        """
            }
        }
    }
}
