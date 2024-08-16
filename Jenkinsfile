pipeline {
  agent { label 'docker' }
  
  triggers {
    GenericTrigger(
     genericHeaderVariables: [
      [key: 'X-GitHub-Event', regexpFilter: '']
     ],
     genericVariables: [
       [key: 'ref', value: '$.ref', regexpFilter: 'refs/heads/'] 
     ],
     token: 'abc123',

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
          echo 'Hello world: $ref'
          echo 'Event: $x_github_event'
        """
      }
    }
  }
  
}
