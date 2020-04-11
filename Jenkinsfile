#!groovy
//JetBrains.dotCover.CommandLineTools\\tools\\dotCover.exe analyze .\\dotcover-coverage.xml /TargetExecutable="NUnit.ConsoleRunner\\tools\\nunit3-console.exe" /TargetArguments="JenknisSetup.Tests\\bin\\Release\\JenknisSetup.Tests.dll --result:nunit2.xml" /TargetWorkingDir="." /Output="dotcover.html" /ReportType="HTML"
//NUnit.ConsoleRunner\\tools\\nunit3-console.exe JenknisSetup.Tests\\bin\\Release\\JenknisSetup.Tests.dll --result:nunit2.xml

pipeline {
    agent any
	
	environment {
        ENV_NAME = "${env.BRANCH_NAME}"
		gitBin = tool name: 'Default', type: 'git'
        msbuildHome = tool name: 'v16.0 (vs2019)', type: 'hudson.plugins.msbuild.MsBuildInstallation'
    }
	

    stages {
	
		stage('Clean up workspace and output build'){
			cleanWorkspace();
		}
					
		stage('NuGet restore') {
			steps {
                bat """
					dotnet --version
                    nuget restore JenknisSetup.sln
                """
			}
        }
		
        stage('Publish') {
			steps {
                bat """
                    echo Building
                    \"${msbuildHome}\" JenknisSetup.sln /p:Configuration=Release  /p:Platform=\"Any CPU\" /p:DeployOnBuild=true /p:PublishProfile=FolderProfile

                """
			}	

        }
        stage('Test') {
			steps {
				script {
					try {
						powershell """
							 nuget install nunit.consolerunner -o . -excludeversion
							 nuget install JetBrains.dotCover.CommandLineTools -o . -excludeversion 
							  
							  
							  
							  JetBrains.dotCover.CommandLineTools\\tools\\dotCover.exe analyze .\\dotcover-coverage.xml /TargetExecutable="NUnit.ConsoleRunner\\tools\\nunit3-console.exe" /TargetArguments="JenknisSetup.Tests\\bin\\Release\\JenknisSetup.Tests.dll --result:nunit2.xml" /TargetWorkingDir="." /Output="codecover\\dotcover.html" /ReportType="HTML"
							
						"""
					}
					finally {
						//step([$class: 'MSTestPublisher', testResultsFile:"nunit2.xml", failOnError: true, keepLongStdio: true])
						publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'codecover', reportFiles: 'dotcover.html', reportName: 'Code Coverage', reportTitles: ''])
						nunit testResultsPattern: 'nunit2.xml'
					}
				}	
			}
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}

def cleanWorkspace()
{
    // Clean the working directory
    def gitBin = tool name: 'Default', type: 'git'
    retry(3)
    {
        bat "\"${gitBin}\" clean -fxd"
    }
}