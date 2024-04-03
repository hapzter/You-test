pipeline {
    agent { label 'master' }
    
    options {
        buildDiscarder(logRotator(daysToKeepStr: '14', artifactDaysToKeepStr: '14'))
        disableConcurrentBuilds()
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Deploy Garage UI') {
            steps {
                script {

                    sh 'printenv'
                }
            }
        }
    }
}
