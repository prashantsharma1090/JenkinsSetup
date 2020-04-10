#!groovy

pipeline {
    agent any
	
	def gitBin = tool name: 'Default', type: 'git'
    def msbuildHome = tool name: 'v16.0 (vs2019)', type: 'hudson.plugins.msbuild.MsBuildInstallation'

    stages {
		
		stage('Checkout') {
                checkout scm

                BRANCH = env.BRANCH_NAME
                BUILD_NUMBER = env.BUILD_NUMBER
                // Get the branch and the commit
                bat "\"${gitBin}\" rev-parse --short HEAD > gitCommit"
                def gitCommit = readFile('gitCommit').trim()
                COMMIT = gitCommit
                DEPLOYANDTEST = false


                bat "echo Building commit ${COMMIT}"
        }
		
		stage('NuGet restore') {
                bat """
                    nuget restore JenknisSetup.sln
                """
        }
		
        stage('Publish') {
                bat """
                    echo Building
                    \"${msbuildHome}\" JenknisSetup.sln /p:Configuration=Release  /p:Platform=\"Any CPU\" /p:DeployOnBuild=true /p:PublishProfile=FolderProfile

                """

        }
        stage('Test') {
            bat """
					 nuget install nunit.consolerunner -o . -excludeversion
                     packages\\NUnit.ConsoleRunner\\tools\\nunit3-console.exe \"JenknisSetup.Tests\\bin\\Release\\JenknisSetup.Tests.dll\" --result:nunit-result.xml;format=nunit2
                """
                nunit testResultsPattern: '*.xml'
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}