pipeline {

    agent {
        kubernetes {
            label 'jenkins-agent'
            defaultContainer 'docker'
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:dind
    command:
    - cat
    tty: true
    securityContext:
      privileged: true
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
"""
        }
    }

    environment {
        IMAGE_TAG = "0.${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'http://192.168.88.20:3000/anderson/simplePythonFlask.git'
            }
        }

        stage('Build') {
            steps {
                sh "docker build -t simple-python-flask:${IMAGE_TAG} ."
            }
        }

        stage('Test') {
            steps {
                sh "docker run -tdi --name simple-python-flask-${IMAGE_TAG} --rm simple-python-flask:${IMAGE_TAG}"
                sh "docker exec simple-python-flask-${IMAGE_TAG} nosetests --with-xunit --with-coverage --cover-package=project test_users.py"
            }
        }
    }

    post {

        success {
            echo "Pipeline executada com sucesso"
        }

        failure {
            echo "Pipeline Falhou"
        }

        cleanup {
            sh "docker stop simple-python-flask-${IMAGE_TAG} || true"
        }
    }
}

