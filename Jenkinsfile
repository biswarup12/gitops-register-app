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
                // Switched to triple double-quotes (""") so Jenkins can inject ${params.IMAGE_TAG}
                sh """
                    echo "IMAGE_TAG received from Pipeline A: ${params.IMAGE_TAG}"

                    echo "Before update:"
                    cat deployment.yaml

                    sed -i -E "s|(image: biswarup1706/register-app-pipeline:).*|\\1${params.IMAGE_TAG}|" deployment.yaml

                    echo "After update:"
                    cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                   git config --global user.name "biswarup12"
                   git config --global user.email "biswarupmondal@gmail.com"
                   git add deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                  sh "git push https://github.com/biswarup12/gitops-register-app main"
                }
            }
        }
    }
}
