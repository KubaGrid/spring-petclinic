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

  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
  }
  
  stages {
    stage("Checkstyle") {
      steps {
        sh './gradlew clean check'
      }
    }
    stage("Test") {
      steps {
        sh './gradlew test -x check'
      }
    }
    stage("Build") {
      agent { label 'docker' }

      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        sh 'docker build -t kkrzych/mr:$x_github_hook_id -f Dockerfile-multi_stage .'
        sh 'docker push kkrzych/rm:$x_github_hook_id'
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: './build/reports/{tests,checkstyle}/*.html,./build/reports/{tests,checkstyle}/*.xml',
      fingerprint: true,
      onlyIfSuccessful: true
    }
  }
  
}
