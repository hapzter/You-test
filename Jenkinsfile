pipeline {
    agent {
    	label 'docker-agent-dotnet'
    }
    parameters {
        string(name: 'url', defaultValue: 'https://github.com/dotnet/samples.git', description: 'Sources to build and test')
        string(name: 'branch', defaultValue: 'main', description: 'Set to coresponding branch')
        booleanParam(name: 'publish', defaultValue: false, description: 'Set to true to run publish stage')
    }
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('DotNet build') {
            steps {
                cleanWs()
                checkout(
                    [$class: 'GitSCM',
                    branches: [[name: params.branch]],
                    userRemoteConfigs: [[url: params.url]]]
                )
                dotnetBuild project: 'csharp/unit-testing/UnitTesting.sln'
            }
        }
        stage('DotNet Unit tests') {
            parallel {
                stage('MSUnit tests') {
                    steps {
			            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
			                dir("csharp/unit-testing/MSTest.Project/") {
                                dotnetTest(
                                    logger: 'trx;LogFileName=msunit_test.trx', 
                                    noBuild: true, 
                                    project: 'MSTest.Project.csproj'
                                )
                            }
                        }
                    }
                }
                stage('NUnit tests') {
                    steps {
                        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
		                    dir("csharp/unit-testing/NUnit.TestProject/") {
                                dotnetTest(
                                    logger: 'trx;LogFileName=nunit_test.trx', 
                                    noBuild: true, 
                                    project: 'NUnit.Project.csproj'
                                )
                            }
                        }
                    }
                }

		        stage('XUnit tests') {
		            steps {
		                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
		                    dir("csharp/unit-testing/XUnit.TestProject/") {
                                dotnetTest(
                                    logger: 'trx;LogFileName=xunit_test.trx', 
                                    noBuild: true, 
                                    project: 'XUnit.Project.csproj'
                                )
                            }
                        }
                    }
 		        }
	        }
        }
	    stage('Publish Test Results') {
	        steps {
                mstest testResultsFile:"**/*.trx", keepLongStdio: true
            }
        }
        stage('DotNet Publish') {
            when {
                anyOf {
                    expression { params.publish == true }
                    expression { currentBuild.currentResult == 'SUCCESS'}
                }
            }
            steps {
                dir("csharp/unit-testing/"){
                    dotnetPublish(
                        noBuild: true,
                        outputDirectory: 'pub', 
                        project: 'UnitTesting.sln', 
                        selfContained: false
                    )
                    zip zipFile: 'UnitTesting.zip', archive: true, dir: 'pub'
                    archiveArtifacts artifacts: 'UnitTesting.zip', fingerprint: true
                }
            }
        }
    }
    post {
        always {
            echo "Build status is ${currentBuild.currentResult}"
        }
    }
}
