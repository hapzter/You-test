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
                def isMasterPipeline = env.BRANCH_NAME == 'master' && env.CHANGE_ID == null
                if  (upstreamCause) { 
                    echo "triggered by Upstream project: ${upstreamCause.getUpstreamProject()}"
                }
                if  (!upstreamCause && isMasterPipeline) { 
                    echo 're-deploy staging' 
                    build 'test-pr/master'
                }
            }
        }
    }
}
