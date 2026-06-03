pipeline {

    agent any



    stages {

        stage('Build Docker Image') {

            steps {

                script {

                    echo 'Building the Docker Image...'

                    // Navigates to the app folder and builds the image

                    sh 'cd app && docker build -t my-portfolio-app .'

                }

            }

        }

        stage('Deploy to Test Environment') {

            steps {

                script {

                    echo 'Deploying container...'

                    // Stops any old container, removes it, and runs the new one on port 9090

                    sh 'docker stop portfolio-container || true'

                    sh 'docker rm portfolio-container || true'

                    sh 'docker run -d -p 9090:80 --name portfolio-container my-portfolio-app'

                }

            }

        }

    }

}
