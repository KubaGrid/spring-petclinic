pipeline {
  agent { label 'docker' }
  
  stages {
    stage("Hello world") {
      steps {
        sh """
          echo 'Hello world'
          echo 'Additional echo statement'
        """
      }
    }
  }
  
}
