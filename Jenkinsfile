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
                        dotnet test MSTest.Project/MSTest.Project.csproj
                        '''
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
                        dotnet test NUnit.TestProject/NUnit.Project.csproj
                        '''
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
                        dotnet test XUnit.TestProject/XUnit.Project.csproj
                        '''
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
}
