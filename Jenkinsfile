def test = ["cucumber" : {gradle("cucumber")}, "jacocoTestReport": {gradle("jacocoTestReport")}, "test": {gradle("test")}]

def gradle(c) {
    return withEnv(["JAVA_HOME=${ tool 'java8'}", "PATH+GRADLE=${tool 'gradle4.6'}/bin"]){
        sh "gradle ${c}"
    }
}

node("${SLAVE}") {
  echo "Hello MNT-Lab"
  stage ("Preparation (Checking out)"){
      echo "Git branch Clone"
      git branch: "master", url: "https://github.com/klmov/Jenkinsfile"
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

  }
  stage ('Packaging and Publishing results'){

  }
  stage ('Asking for manual approval'){

  }
  stage ('Deployment'){

  }
}
