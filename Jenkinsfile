@Library("shared") _
pipeline {
    
    agent { label "vinod" }
        
    stages {
        stage('Hello'){
            steps{
                script{
                    hello()
                }
            }
        }
        
        stage('Code') {
            steps {
                script{
                clone('https://github.com/LondheShubham153/django-notes-app.git','main')
                }
            }
        }

        stage('Build') {
            steps {
                script{
                    build()
                }
            }
        }

        stage('Test') {
            steps {
                echo 'This is Testing the code'

                sh 'docker compose config'
            }
        }
        
        stage("Push To Dockerhub") {
        steps {
            echo "This is pushing the image to Docker Hub"
    
            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerHubCred',
                    usernameVariable: 'dockerHubUser',
                    passwordVariable: 'dockerHubPass'
                )
            ]) {
    
                sh 'echo "$dockerHubPass" | docker login -u "$dockerHubUser" --password-stdin'
    
                sh 'docker image tag django_app:latest "$dockerHubUser"/notes-app:latest'
    
                sh 'docker push "$dockerHubUser"/notes-app:latest'
            }
        }
    }


        stage('Deploy') {
            steps {
                echo 'This is Deploying the application'

                sh 'docker compose down'
                sh 'docker compose up -d'

                echo 'Waiting for containers...'
                sleep 15

                sh 'docker compose ps'

                echo 'Checking Django logs...'
                sh 'docker compose logs --tail=50 django_app'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
            sh 'docker compose ps || true'
            sh 'docker compose logs --tail=100 django_app || true'
        }
    }
}
