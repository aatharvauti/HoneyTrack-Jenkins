pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'echo { >> /etc/docker/daemon.json'
                    sh 'echo "dns": ["8.8.8.8", "8.8.4.4"] >> /etc/docker/daemon.json'
                    sh 'echo } >> /etc/docker/daemon.json'
                    sh 'docker build -f docker/Dockerfile -t auti/ht .'
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    // Run Docker container and capture container ID

                    def containerId = sh(script: "docker container run --restart=always -d --name ssh-server -p 999:22 auti/honeytrack", returnStdout: true).trim()

                    // Wait for the container to start
                    sleep 10

                    // Execute command inside the Docker container
                    sh "docker exec ${containerId} tail -n 1 ~/start.sh | grep 'good to go!'"
                }
            }
        }
    }
    post {
        always {
            // Clean up Docker resources
            script {
                sh 'docker stop ssh-server'
                sh 'docker rm ssh-server'
            }
        }
    }
}
