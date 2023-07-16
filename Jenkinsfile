def branch = "main"
def repo = "git@github.com:MuhSatriyo/housy-frontend.git"
def cred = "cicd"
def dir = "~/housy-frontend"
def server = "tesgtw@103.82.92.144"
def imagename = "housy-fe"
def dockerusername = "muhsatriyo"

pipeline {
    agent any

    stages {
        stage('Pull From Repository') {
            steps {
                script {
                    sshagent(credentials: [cred]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${server} <<EOF
                            cd ${dir}
                            git remote add origin ${repo} || git remote set-url origin ${repo}
                            git pull origin ${branch}
                            exit
EOF
                        """
                    }
                }
            }
        }

        stage('Dockerize') {
            steps {
                script {
                    sshagent(credentials: [cred]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${server} <<EOF
                            cd ${dir}
                            docker build -t ${imagename}:latest .
                            exit
EOF
                        """
                    }
                }
            }
        }

        stage('Deploy Docker') {
            steps {
                script {
                    sshagent(credentials: [cred]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${server} <<EOF
                            cd ${dir}
                            docker container stop ${imagename}
                            docker container rm ${imagename}
                            docker run -d -p 3000:3000 --name="${imagename}" ${imagename}:latest
                            docker container stop ${imagename}
                            docker container rm ${imagename}
                            exit
EOF
                        """
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    sshagent(credentials: [cred]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${server} <<EOF
                            docker tag ${imagename}:latest ${dockerusername}/${imagename}:latest
                            docker image push ${dockerusername}/${imagename}:latest
                            docker image rm ${dockerusername}/${imagename}:latest
                            docker image rm ${imagename}:latest
                            exit
EOF
                        """
                    }
                }
            }
        }
    }
}
