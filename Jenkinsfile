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
      when {
        environment name: 'x_github_event', value 'pull_request'
      }
    }
    stage("Test") {
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          sh './gradlew test -x check'
        }
      }
      when {
        environment name: 'x_github_event', value 'pull_request'
      }
    }
    stage("Build") {
      agent { label 'docker' }

      steps {
        sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW &>/dev/null'
        sh 'docker build -t kkrzych/mr:$x_github_hook_id -f Dockerfile-multi_stage . &>/dev/null'
      }
      when {
        return env.x_github_event in ['pull_request', 'push']
      }
    }
    stage("Deploy") {
      agent { label 'docker'}
      steps {
        sh 'docker push kkrzych/mr:$x_github_hook_id'
      }
      when {
        return env.x_github_event in ['pull_request', 'push']
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: './build/reports/tests/test, ./build/reports/tests/test, ./build/reports/checkstyle/*.html, ./build/reports/checkstyle/*.xml',
      fingerprint: true,
      onlyIfSuccessful: true
    }
  }
  
}
