podTemplate(containers: [

    containerTemplate(
        name: 'maven',
        image: 'maven:3.8.1-jdk-8',
        command: 'sleep',
        args: '99d'
    ),

    containerTemplate(
        name: 'docker',
        image: 'docker:20.10.24',
        command: 'sleep',
        args: '99d',
        ttyEnabled: true,
        privileged: true
    )

],
volumes: [
    hostPathVolume(
        hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'
    )
]) {

    node(POD_LABEL) {
        container('docker') {
            stage("Clona Git") {
                git 'http://192.168.88.20:3000/anderson/simplePythonFlask.git'
            }

            stage("Build") {
                sh "docker build -t simple-python-flask:${BUILD_ID} ."
            }

            stage("Teste") {
                sh "docker run -td --name simple-python-flask-${BUILD_ID} --rm simple-python-flask:${BUILD_ID}"
                sh "docker exec simple-python-flask-${BUILD_ID} nosetests --with-xunit --with-coverage --cover-package=project test_users.py"
            }
        }
    }
}

