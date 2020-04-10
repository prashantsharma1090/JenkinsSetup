#!groovy

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
				bat """
					 .nuget\\NuGet.exe install nunit.consolerunner -o . -excludeversion

                     .\\NUnit.ConsoleRunner\\tools\\nunit3-console.exe \"JenknisSetup.Tests\\bin\\Release\\JenknisSetup.Tests.dll\" --result:nunit2.xml

                """
                nunit testResultsPattern: '*.xml'
			}	
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}