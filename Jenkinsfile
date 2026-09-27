```groovy
pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        APP_NAME = "register-app-pipeline"
    }

    parameters {
        string(
            name: 'IMAGE_TAG',
            defaultValue: '',
            description: 'Docker image tag received from CI pipeline'
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

        stage("Update the Deployment Tags") {
            steps {
                sh '''
                    echo "Updating deployment with image tag: ${IMAGE_TAG}"

                    cat deployment.yaml

                    sed -i "s#${APP_NAME}:.*#${APP_NAME}:${IMAGE_TAG}#g" deployment.yaml

                    echo "Updated deployment.yaml:"
                    cat deployment.yaml
                '''
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh '''
                    git config user.name "biswarup12"
                    git config user.email "biswarupmondal2012@gmail.com"

                    git add deployment.yaml

                    git commit -m "Updated Deployment Manifest"
                '''

                withCredentials([
                    gitUsernamePassword(
                        credentialsId: 'github',
                        gitToolName: 'Default'
                    )
                ]) {
                    sh '''
                        git push https://github.com/biswarup12/gitops-register-app
                    '''
                }
            }
        }
    }
}
```
