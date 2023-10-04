def spare_checkout() {
    checkout([$class: 'GitSCM', 
        branches: [[name: '*/${branch}']],
            extensions: [
                [$class: 'SparseCheckoutPaths', 
                sparseCheckoutPaths:[[$class:'SparseCheckoutPath', path:'/csharp/unit-testing']]]
                ],
            userRemoteConfigs: [[url: 'https://github.com/dotnet/samples.git']]])
}

pipeline {    
    agent {   
        node {
            label 'docker-agent-dotnet'
            }
      }               
                               
    stages {
        stage('Build') {
            steps {
                spare_checkout()
                sh'''
                cd csharp/unit-testing
                dotnet build UnitTesting.sln
                '''
            }       
        }       
        stage('Run parallel Tests') {
            parallel {
                stage('MSTest') {
                    agent {
                        node {
                            label 'docker-agent-dotnet'
                        }
                    }
                    steps {
                        spare_checkout()
                        sh'''
                        cd csharp/unit-testing
                        dotnet test --logger "trx;LogFileName=unit_test.trx" \
                            MSTest.Project/MSTest.Project.csproj
                        '''
                    }
                    post {
                        always {
                            mstest testResultsFile:"**/*.trx", keepLongStdio: true
                        }
                    }
                }
                stage('NUnit') {
                    agent {
                        node {
                            label 'docker-agent-dotnet'
                        }
                    }
                    steps {
                        spare_checkout()
                        sh'''
                        cd csharp/unit-testing
                        dotnet test --logger "trx;LogFileName=unit_test.trx" \
                            NUnit.TestProject/NUnit.Project.csproj
                        '''
                    }
                    post {
                        always {
                            mstest testResultsFile:"**/*.trx", keepLongStdio: true
                        }
                    }
                }
                stage('XUnit') {
                    agent {
                        node {
                            label 'docker-agent-dotnet'
                        }
                    }
                    steps {
                        spare_checkout()
                        sh'''
                        cd csharp/unit-testing
                        dotnet test --logger "trx;LogFileName=unit_test.trx" \
                            XUnit.TestProject/XUnit.Project.csproj
                        '''}
                    post {
                        always {
                            mstest testResultsFile:"**/*.trx", keepLongStdio: true
                        }
                    }
                }
            }    
        }       
        stage('Publish') {
                steps {
                        echo "Publishing... if any"
                }        
        }
    }                 
}                        spare_checkout()


