podTemplate(containers: [
    containerTemplate(name: 'maven', image: 'maven:3.8.1-jdk-8', command: 'sleep', args: '99d'),
    containerTempplate(
        name: 'docker',
        image: 'docker:dind',
        command: 'sleep',
        args: '99d',
        ttyEnabled: true,
        prviliged: true
    )],
  volumes: [ hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
){

    environment {
        IMAGE_TAG="0.${BUILD_ID}"
    }
    
    node(POD_LABEL)
    {
        container('docker'){
            git 'http://192.168.88.20:3000/anderson/simplePythonFlask.git'
        }
    }

    stages{
        stage('Build') {
            steps{
                sh "docker build -t simple-python-flask:${IMAGE_TAG} ."
            }
         }
         stage ("Teste") {
             steps{
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
        sh "docker stop simple-python-flask-${IMAGE_TAG}"
        }
    }
}
