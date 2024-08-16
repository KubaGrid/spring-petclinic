pipeline {
  agent { label 'docker' }
  
  triggers {
    GenericTrigger(
     genericHeaderVariables: [
      [key: 'X-GitHub-Event', regexpFilter: '']
     ],
     token: credentials('token'),

     causeString: 'Triggered on $x_github_event',
      
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
          echo 'Event: $x_github_event'
        """
      }
    }
  }
  
}
