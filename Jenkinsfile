podTemplate(
  containers: [

    containerTemplate(
      name: 'docker',
      image: 'docker:24.0.7-cli',
      command: 'sleep',
      args: '99d',
      ttyEnabled: true
    ),

    containerTemplate(
      name: 'sonar',
      image: 'sonarsource/sonar-scanner-cli:latest',
      command: 'sleep',
      args: '99d'
    ),
    containerTemplate(
    name: 'kubectl',
    image: 'alpine',
    command: 'sleep',
    args: '99d',
    )
  ],
  volumes: [
    hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ]
) {

  node(POD_LABEL) {

    container('docker') {

      stage("Clona Git") {
        git 'http://192.168.88.20:3000/anderson/simplePythonFlask.git'
      }

      stage("Build") {
        sh "docker build -t simple-python-flask:${BUILD_ID} ."
      }

      stage("Teste") {
        sh "docker run -tdi --name simple-python-flask-${BUILD_ID} --rm simple-python-flask:${BUILD_ID}"
        sh "docker exec simple-python-flask-${BUILD_ID} nosetests --with-xunit --with-coverage --cover-package=project test_users.py"
        sh "docker cp simple-python-flask-${BUILD_ID}:/courseCatalog/nosetests.xml ."
        sh "docker stop simple-python-flask-${BUILD_ID}"
      }

      sh "docker tag simple-python-flask:${BUILD_ID} 192.168.88.20:8082/simple-python-flask:${BUILD_ID}"
    }

    container('sonar') {

      stage('SonarQube Analysis') {

        withSonarQubeEnv('SonarQube') {

          withCredentials([string(credentialsId: 'd3c0b5db-aa6b-435e-86c5-451c4d7d0ce3', variable: 'SONAR_TOKEN')]) {

            sh """
              sonar-scanner \
              -Dsonar.projectKey=CourseCatalog \
              -Dsonar.host.url=http://192.168.88.20:9000 \
              -Dsonar.login=$SONAR_TOKEN \
              -Dsonar.sources=. \
              -Dsonar.python.coverage.reportPaths=nosetests.xml
            """

          }
        }
      }

     container('kubectl'){
        stage('deploy image'){
        withKubeConfig([credentialsID: 'k3s-serviceaccount',
                        serverUrl: 'http://192.168.88.30:6443',
       ]){
       sh 'apk update && apk add --no-cache curl'
       sh 'curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"'
       sh 'chmod +x ./kubectl && mv ./kubectl /us/local/bin/kubectl'
       sh 'sleep 5'
       sh 'kubectl set image deployment/web simplepythonflask=192.168.88.20:8082/simple-python-flask:${BUILD_ID} -n homolog'
       sh 'sleep 4'
       }
       
}

 }  
    }

  }
}

