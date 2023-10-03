pipeline {
  agent {
    node {
      label 'Samsung-node'
    }

  }
  stages {
    stage('Checkout') {
      steps {
        git(url: 'https://github.com/dotnet/samples/blob/', branch: '${branch}')
      }
    }

  }
  environment {
    Branch = 'main'
    Publish = ''
  }
}