pipeline {
    agent any
    
    options {
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    echo 'environment multibranch'
                    sh 'printenv'
                }
            }
        }
    }
    post {
        success {
            script {
                def upstreamCause = currentBuild.rawBuild.getCause(hudson.model.Cause$UpstreamCause)
                if  (upstreamCause) { 
                    echo "triggered by Upstream project: ${upstreamCause.getUpstreamProject()}"
                }
                if  (!upstreamCause) { 
                    echo 're-deploy staging' 
                    build 'test-pr/master'
                }
            }
        }
    }
}
