pipeline{
    agent any
    stages{
	stage('SCA-->Snyk') {
            agent any
            steps{
				checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/tooladi/adi_repo_code.git']]])
				snykSecurity failOnIssues: false, organisation: '8e4b6a20-7d7e-42b6-a1f9-69503cacee7d', snykInstallation: 'snykKey', snykTokenId: 'snykSecrectS'
                }
        }
stage('snyk-jira') {
		steps {
			script{
				catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') 
				{
					bat 'C:\\Users\\Administrator\\Downloads\\snyk-jira-sync-win.exe --orgID=8e4b6a20-7d7e-42b6-a1f9-69503cacee7d --token=5eaeb723-1ce3-420b-9045-5d93d21a7206 --jiraProjectKey=AD --configFile=true --jiraTicketType=Task --projectID=e83fb563-2c5c-4167-9af7-2fab7c94967e'
					bat 'exit 0'
				}
			}
		}
	}
	}
}
