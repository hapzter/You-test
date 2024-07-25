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
                    def triggeredBy = triggerCauses.any { it.toString().contains("BuildUpstreamCause")}
                    echo 'triggered by: ' + triggeredBy
                    echo 'environment multibranch merge'
                    sh 'printenv'
                }
            }
        }
    }
}
