@Library("shared") _

pipeline {
    agent { label "micah" }

    triggers {
        githubPush()
    }

    stages {

        stage("Hello") {
            steps {
                script {
                    hello()
                }
            }
        }

        stage("Code") {
            steps {
                script {
                    clone("https://github.com/micahjoseph1234/django-notes-app", "main")
                }
            }
        }

        stage("Build") {
            steps {
                script {
                    docker_build("notes-app", "latest", "micahjoseph123")
                }
            }
        }

        stage("Push To Docker Hub") {
            steps {
                script {
                    docker_push("notes-app", "latest", "micahjoseph123")
                }
            }
        }

        stage("Deploy") {
            steps {
                sh '''
                docker rm -f notes-app mysql || true
                docker network rm notes-network || true

                docker network create notes-network

                docker run -d \
                  --name mysql \
                  --network notes-network \
                  -e MYSQL_ROOT_PASSWORD=root \
                  -e MYSQL_DATABASE=notesdb \
                  -e MYSQL_USER=notesuser \
                  -e MYSQL_PASSWORD=notespass \
                  mysql:8

                sleep 40

                docker run -d \
                  --name notes-app \
                  --network notes-network \
                  -p 8000:8000 \
                  -e DB_NAME=notesdb \
                  -e DB_USER=notesuser \
                  -e DB_PASSWORD=notespass \
                  -e DB_HOST=mysql \
                  -e DB_PORT=3306 \
                  notes-app:latest
                '''
            }
        }
    }
}
