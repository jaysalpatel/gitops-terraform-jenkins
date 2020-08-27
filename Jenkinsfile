// Jenkinsfile
String credentialsId = 'awsCredentials'
//try catch block, checkout repo
try {
  stage('checkout') {
    node {
      //clean workspace
      cleanWs()
      checkout scm
    }
  }

  // Run terraform init
  stage('init') {
    node {
      ///how to access AWS access keys with environment variables
      withCredentials([[
        $class: 'AmazonWebServicesCredentialsBinding',
        credentialsId: credentialsId,
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
      ]]) {
        ansiColor('xterm') {
          sh 'terraform init'
        }
      }
    }
  }

  // Run terraform plan
  stage('plan') {
    node {
      withCredentials([[
        $class: 'AmazonWebServicesCredentialsBinding',
        credentialsId: credentialsId,
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
      ]]) {
        ansiColor('xterm') {
          sh 'terraform plan'
        }
      }
    }
  }
    ///only if the branch name is master then run terraform apply and do not want to pull request to run terraform apply and whens approved and merged then it will apply
  if (env.BRANCH_NAME == 'master') {

    // Run terraform apply
    stage('apply') {
      node {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: credentialsId,
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          ansiColor('xterm') {
            sh 'terraform apply -auto-approve'
          }
        }
      }
    }

    // Run terraform show
    stage('show') {
      node {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: credentialsId,
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          ansiColor('xterm') {
            sh 'terraform show'
          }
        }
      }
    }
  }
  currentBuild.result = 'SUCCESS'
}
catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException flowError) {
  currentBuild.result = 'ABORTED'
}
catch (err) {
  currentBuild.result = 'FAILURE'
  throw err
}
finally {
  if (currentBuild.result == 'SUCCESS') {
    currentBuild.result = 'SUCCESS'
  }
}
