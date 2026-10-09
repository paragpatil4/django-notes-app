pipeline{
    agent { label 'dev-server' }
    
    stages{
        stage("Code clone"){
            steps{
                sh "whoami"
                git branch: 'main',
                    url: 'https://github.com/paragpatil4/django-notes-app.git'
            }
        }
        stage("Code Build"){
            steps{
                sh 'docker build -t notes-app:latest .'
            }
        }
        stage("Push to DockerHub"){
            steps{
                withCredentials([
            usernamePassword(
                credentialsId: 'dockerHubCreds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
                )
             ]) {
            sh '''
                echo "$DOCKER_PASS" | docker login \
                    -u "$DOCKER_USER" --password-stdin

                docker tag notes-app:latest \
                    "$DOCKER_USER/notes-app:latest"

                docker push "$DOCKER_USER/notes-app:latest"

                docker logout
            '''
            }
            }
        }
        
        stage('Deploy') {
            steps {
                sh '''
                docker stop notes-app || true
                docker rm notes-app || true

                docker run -d \
                    --name notes-app \
                    -p 8000:8000 \
                    notes-app:latest
                '''
            }
        }
   
    }
}
