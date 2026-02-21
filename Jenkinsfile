pipeline {

    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:

  - name: docker
    image: docker:24.0
    command:
    - cat
    tty: true
    env:
    - name: DOCKER_HOST
      value: tcp://localhost:2375

  - name: dind
    image: docker:24.0-dind
    securityContext:
      privileged: true
    env:
    - name: DOCKER_TLS_CERTDIR
      value: ""
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
                container('docker') {
                    sh 'docker info'
                    sh "docker build -t simple-python-flask:${IMAGE_TAG} ."
                }
            }
        }

        stage('Test') {
            steps {
                container('docker') {
                    sh "docker run -td --name simple-python-flask-${IMAGE_TAG} simple-python-flask:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        cleanup {
            container('docker') {
                sh "docker stop simple-python-flask-${IMAGE_TAG} || true"
            }
        }
    }
}

