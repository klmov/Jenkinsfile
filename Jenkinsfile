def test = ["cucumber" : {gradle("cucumber")}, "jacocoTestReport": {gradle("jacocoTestReport")}, "test": {gradle("test")}]
def STUDENT_NAME = "kklimov"
def JOB_NAME = "MNTLAB-${STUDENT_NAME}-child1-build-job"
def GITHUB_REPOSITORY = "https://github.com/klmov/Jenkinsfile"
def gradle(c) {
    return withEnv(["JAVA_HOME=${ tool 'java8'}", "PATH+GRADLE=${tool 'gradle4.6'}/bin"]){
        sh "gradle ${c}"
    }
}
def NexusPull(artefactName){
  repository = "Maven_Artefacts"
  groupId = "Artefacts"
  artefName = "pipeline"
  def addr = "http://EPBYMINW2033.minsk.epam.com:8081/repository/${repository}/${groupId}/${artefName}/${BUILD_NUMBER}/${artefactName}"
  println addr
  def authString = "YWRtaW46YWRtaW4xMjM=" //Not really safe :(
  def conn = addr.toURL().openConnection()
  conn.setDoOutput(true);
  conn.setRequestMethod("POST")
  conn.setRequestProperty( "Authorization", "Basic ${authString}")
  conn.setRequestProperty("Content-Type", "application/x-gzip")
  def downFile = new DataOutputStream(conn.outputStream)
  def filess = findFiles(glob: '*.tar.gz')
  println files[0].name
  downFile.write(filess.getBytes())
  downFile.close()
  println http.responseCode
}
def artfname = "pipeline-${STUDENT_NAME}-${BUILD_NUMBER}.tar.gz"

node("${SLAVE}") {
  echo "Hello MNT-Lab"
  stage ("Preparation (Checking out)"){
      cleanWs()
      echo "Git branch Clone"
      git branch: 'master', url: GITHUB_REPOSITORY
  }
  stage ("Building code") {
      echo "Starting Build section"
      tool name: 'gradle4.6', type: 'gradle'
      tool name: 'java8', type: 'jdk'
      gradle("build")
      echo "Builded"
  }
  stage ("Testing code") {
    parallel test
  }
  stage ('Triggering job and fetching artefact after finishing'){
    build job: JOB_NAME, parameters: [[$class: 'StringParameterValue', name: 'BRANCH_NAME', value: STUDENT_NAME]]
    copyArtifacts filter: '*.tar.gz', fingerprintArtifacts: true, projectName: JOB_NAME, selector: lastSuccessful()
  }
  stage ('Packaging and Publishing results'){
    sh """ tar -xvf *tar.gz
           tar -czf ${artfname} jobs.groovy Jenkinsfile  output.txt -C build/libs/ \$JOB_NAME.jar"""
    script {
      NexusPull(artfname)
    }
    archiveArtifacts artfname
  }
  stage ('Asking for manual approval'){

  }
  stage ('Deployment'){

  }
}
