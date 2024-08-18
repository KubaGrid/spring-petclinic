pipeline {
  agent any

  triggers {
    GenericTrigger(
     genericHeaderVariables: [
      [key: 'X-GitHub-Event', regexpFilter: ''],
      [key: 'X-GitHub-Hook-ID', regexpFilter: '']
     ],
     genericVariables: [
       [key: 'ref', value: '$.ref', regexpFilter: '^(refs/heads/|refs/remotes/origin/)'] 
     ],
     token: 'abc123',

     causeString: 'Triggered on $x_github_event',
      
     printContributedVariables: true,

     silentResponse: false,
     
     shouldNotFlatten: false,
    )
  }
  
  stages {
    stage("Checkstyle") {
      steps {
        sh './mvnw site'
      }
    }
    stage("Test") {
      steps {
        sh './mvnw test'
      }
    }
    stage("Build") {
      agent { label 'docker' }

      steps {
        sh 'docker build -t kkrzych/mr:$x_github_hook_id'
        sh 'docker push kkrzych/rm:$x_github_hook_id'
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: './target/{site,surefire-reports}/*.html, ./target/{site,surefire-reports}/*.xml'
    }
  }
  
}
