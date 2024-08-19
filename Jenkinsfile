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
      when {
        expression { return env.x_github_event == 'pull_request' }
      }
      steps {
        sh './gradlew clean check -x test'
      }
    }
    stage("Test") {
      when {
        expression { env.x_github_event == 'pull_request' }
      }
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          sh './gradlew test -x check'
        }
      }
    }
    stage("Build") {
      agent { label 'docker' }

      when {
        expression { env.x_github_event in ['pull_request', 'push'] }
      }

      steps {
        sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW &>/dev/null'
        sh 'docker build -t kkrzych/mr:$x_github_hook_id -f Dockerfile-multi_stage . &>/dev/null'
      }
    }

    stage("Deploy to staging") {
      agent { label 'docker'}

      when {
        allOf{
          expression { env.x_github_event == 'pull_request' }
        }
      }

      steps {
        sh 'docker push kkrzych/mr:$x_github_hook_id'
      }
    }
  
    stage("Deploy") {
      agent { label 'docker'}

      when {
        allOf{
          expression { env.x_github_event == 'push' }
          expression { env.ref == 'main' }
        }
      }

      steps {
        sh 'docker push kkrzych/main:$GIT_COMMIT'
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: 'build/reports/tests/test, build/reports/tests/test, build/reports/checkstyle/*.html, build/reports/checkstyle/*.xml',
      fingerprint: true,
      onlyIfSuccessful: true
    }
  }
  
}
