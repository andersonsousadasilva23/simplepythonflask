pipeline {
    agent any
    enviroment {
        IMAGE-TAG="0.${BUILD-ID}"
    }
    stages{
        stage('Build') {
            steps{
                sh "docker build -t simple-python-flask:${IMAGE-TAG} ."
            }
         stage ("Teste") {
             steps{
                 sh "docker run -tdi --name simple-python-flask-${IMAGE-TAG} --rm simple-python-flask:${IMAGE-TAG}"
                 sh "docker exec -ti simple-python-flask-${IMAGE-TAG} nosetests --with-xunit --with-coverage --cover-package=project test_users.py"
	     }
         }
    }
    }
}
