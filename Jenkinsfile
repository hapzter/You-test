pipeline {
    agent any
    
    options {
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    def triggerCauses = currentBuild.rawBuild.getCauses()
                    def triggeredByUpstream = triggerCauses.any { it.toString().contains("BuildUpstreamCause")}
                    if  triggeredByUpstream { echo 'triggered by Upstream project' }
                    if  !triggeredByUpstream { echo 're-deploy staging' }
                    echo 'environment multibranch merge'
                    sh 'printenv'
                }
            }
        }
    }
}
