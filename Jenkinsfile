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
        sh './gradlew clean check -x test'
      }
    }
    stage("Test") {
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          sh './gradlew test -x check'
        }
      }
    }
    stage("Build") {
      agent { label 'docker' }

      steps {
        sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW &>/dev/null'
        sh 'docker build -t kkrzych/mr:$x_github_hook_id -f Dockerfile-multi_stage . &>/dev/null'
      }
    }
    stage("Deploy") {
      agent { label 'docker'}
      steps {
        sh 'docker push kkrzych/mr:$x_github_hook_id'
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: './build/reports/tests/*.xml ./build/reports/tests/*.html ./build/reports/checkstyle/*.html, ./build/reports/checkstyle/*.xml',
      fingerprint: true,
      onlyIfSuccessful: true
    }
  }
  
}
