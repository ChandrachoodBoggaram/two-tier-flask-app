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
