podTemplate(
  containers: [

    containerTemplate(
      name: 'maven',
      image: 'maven:3.9.6-eclipse-temurin-17',
      command: 'sleep',
      args: '99d',
      ttyEnabled: true
    ),

    containerTemplate(
      name: 'python',
      image: 'python:3.11',
      command: 'sleep',
      args: '99d',
      ttyEnabled: true
    ),

    containerTemplate(
      name: 'docker',
      image: 'docker:20.10.24',
      command: 'sleep',
      args: '99d',
      ttyEnabled: true
    )
  ],

  volumes: [
    hostPathVolume(
      hostPath: '/var/run/docker.sock',
      mountPath: '/var/run/docker.sock'
    )
  ]
) {

  node(POD_LABEL) {

    stage('Clone') {
      git 'http://192.168.88.20:3000/anderson/simplePythonFlask.git'
    }

    stage('Build Python') {
      container('python') {
        sh 'python --version'
        sh 'pip install -r requirements.txt || true'
      }
    }

    stage('Simular Maven') {
      container('maven') {
        sh 'mvn -version'
      }
    }

    stage('Docker Build') {
      container('docker') {
        sh "docker build -t simple-python-flask:${BUILD_ID} ."
      }
    }

    stage('Docker Run') {
      container('docker') {
        sh "docker run -d --name simple-python-flask-${BUILD_ID} simple-python-flask:${BUILD_ID}"
      }
    }

    stage('Cleanup') {
      container('docker') {
        sh "docker stop simple-python-flask-${BUILD_ID} || true"
        sh "docker rm simple-python-flask-${BUILD_ID} || true"
      }
    }
  }
}

