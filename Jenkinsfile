pipeline {
    agent { label 'dev' }
    stages {
        stage("Clone") {
            steps {
                git url: "https://github.com/ChandrachoodBoggaram/two-tier-flask-app.git", branch: "main"
                echo "Code has been cloned successfully"
            }
        }
        stage("Build") {
            steps {
                sh "docker build -t flask-app ."
            }
        }
        stage("Push") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "dockerhub-creds",
                    usernameVariable: "USERNAME",
                    passwordVariable: "PASSWORD"
                )]) {
                    sh "docker login -u $USERNAME -p $PASSWORD"
                    sh "docker image tag flask-app $USERNAME/flask-app:latest"
                    sh "docker push $USERNAME/flask-app:latest"
                    echo "Image has been pushed to docker hub"
                }
            }
        }
        stage("Deploy") {
            steps {
                sh "docker compose up -d"
            }
        }
    }
    
    post {
        success {
            emailext (
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Good news!\n\nJob '${env.JOB_NAME} [${env.BUILD_NUMBER}]' completed successfully.\nCheck console: ${env.BUILD_URL}",
                to: "chandrachoodboggaram@gmail.com"
            )
        }

        failure {
            emailext (
                subject: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Oops!\n\nJob '${env.JOB_NAME} [${env.BUILD_NUMBER}]' has failed.\nCheck details here: ${env.BUILD_URL}",
                to: "chandrachoodboggaram@gmail.com"
            )
        }
        always {
            cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true,
                    patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                               [pattern: '.propsfile', type: 'EXCLUDE']])
        }
        
    }
}
