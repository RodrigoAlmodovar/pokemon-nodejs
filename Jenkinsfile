podTemplate(
    label: 'jenkinspod', 
    inheritFrom: 'default',
    containers: [
        containerTemplate(
            name: 'docker', 
            image: 'docker:18.02',
            ttyEnabled: true,
            command: 'cat'
        )
    ],
    volumes: [
        hostPathVolume(
            hostPath: '/var/run/docker.sock',
            mountPath: '/var/run/docker.sock'
        )
    ]
) {
    node('jenkinspod') {
        def commitId
        stage ('Extract') {
            checkout scm
            commitId = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
        }
        def repository
        stage ('Build') {
            container ('docker') {
                repository = "h4055592/pokemon-nodejs"
                sh "docker build -t ${repository}:${commitId} ."
            }
        }
        stage("push") {
            container ('docker') {
              withCredentials([[
                  $class: 'UsernamePasswordMultiBinding', 
                  credentialsId: 'docker_hub',
                  usernameVariable: 'Username', passwordVariable: 'Password'
              ]]) {
                  sh "docker login -u $Username -p $Password"
                  sh "docker push ${repository}:${commitId}"
              }
            }
        }
    }
}
