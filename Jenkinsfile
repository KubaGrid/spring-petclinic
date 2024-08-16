pipeline {
  agent { label 'docker' }

  triggers {
    GenericTrigger(
     genericHeaderVariables: [
      [key: 'X-GitHub-Event', regexpFilter: '^(ping|push|pull\_request)$']
     ],

     causeString: 'Triggered on $X-GitHub-Event',
      
     printContributedVariables: true,
     printPostContent: true,

     silentResponse: false,
     
     shouldNotFlatten: false,
    )
  }
  
  stages {
    stage("Hello world") {
      steps {
        sh """
          echo 'Hello world'
          echo 'Event: $X-GitHub-Event'
        """
      }
    }
  }
  
}
