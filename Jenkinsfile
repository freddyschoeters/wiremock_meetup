node {
    def stubName = "stubs"
    def stubImage = "${stubName}-image"
    def stubPort = "9002"
    stage("checkout") {
        checkout scm
    }
    stage("build docker image") {
        sh "docker build -t ${stubImage} ."
    }
    stage("stop stub") {
        sh "docker rm -f ${stubName} || true"
    }
    stage("start new version") {
        sh "docker run -d --rm --name ${stubName} -p ${stubPort}:8080 ${stubImage} --verbose --global-response-templating --jetty-header-buffer-size 16384"
    }
}
