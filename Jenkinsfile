pipeline {
    agent any
    
    options {
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    echo 'environment multibranch merge'
                    sh 'printenv'
                }
            }
        }
    }
}
