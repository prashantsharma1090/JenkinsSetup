#!groovy
//JetBrains.dotCover.CommandLineTools\\tools\\dotCover.exe analyze .\\dotcover-coverage.xml /TargetExecutable="NUnit.ConsoleRunner\\tools\\nunit3-console.exe" /TargetArguments="JenknisSetup.Tests\\bin\\Release\\JenknisSetup.Tests.dll --result:nunit2.xml" /TargetWorkingDir="." /Output="dotcover.html" /ReportType="HTML"

pipeline {
    agent any
	
	environment {
        ENV_NAME = "${env.BRANCH_NAME}"
		gitBin = tool name: 'Default', type: 'git'
        msbuildHome = tool name: 'v16.0 (vs2019)', type: 'hudson.plugins.msbuild.MsBuildInstallation'
    }
	

    stages {
					
		stage('NuGet restore') {
			steps {
                bat """
					dotnet --version
                    .nuget\\NuGet.exe restore JenknisSetup.sln
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
				powershell """
					 .nuget\\NuGet.exe install nunit.consolerunner -o . -excludeversion
					  .nuget\\NuGet.exe install JetBrains.dotCover.CommandLineTools -o . -excludeversion 
					  
                      NUnit.ConsoleRunner\\tools\\nunit3-console.exe JenknisSetup.Tests\\bin\\Release\\JenknisSetup.Tests.dll --result:nunit2.xml
					
                """
               
			    nunit testResultsPattern: 'nunit2.xml'
				//step([$class: 'MSTestPublisher', testResultsFile:"nunit2.xml", failOnError: true, keepLongStdio: true])
				publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'TestResults', reportFiles: 'dotcover.html', reportName: 'Code Coverage', reportTitles: ''])
			}	
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}