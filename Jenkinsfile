pipeline {
  agent { label 'docker' }
  
  stages {
    stage("Hello world") {
      sh """
        echo 'Hello world'
        echo 'Additional echo statement'
      """
    }
  }
  
}
