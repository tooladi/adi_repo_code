pipeline{
    agent any
    stages{
	stage('Pre-Commit--> Trufflehog'){
            agent {label 'linagent2'}
	        options {
                skipDefaultCheckout()
                }
			steps{
				sh 'echo hello'
				sh 'docker pull gesellix/trufflehog'
				sh 'docker run gesellix/trufflehog --json --regex https://github.com/tooladi/adi_repo_code.git > result'
				sh 'cat result'
              }
        }
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
					bat 'C:\\Users\\Administrator\\Downloads\\snyk-jira-sync-win.exe --orgID=8e4b6a20-7d7e-42b6-a1f9-69503cacee7d --token=5eaeb723-1ce3-420b-9045-5d93d21a7206 --jiraProjectKey=AD --configFile=true --jiraTicketType=Task --projectID=c76b34a7-cf9c-413a-a8a8-826bdeb61a66'
					bat 'exit 0'
				}
			}
		}
	}
        stage ('DAST-->ZAP')
                {
			agent any
    			steps
                               {
         			bat 'java -jar C:\\Users\\Administrator\\Downloads\\OWASP\\ZedAttackProxy\\zap-2.10.0.jar -quickurl http://demo.testfire.net -quickout C:\\result_1.html -quickprogress -cmd'
    				}
		}
        stage("Snyk-Container Scan")
	{
    		agent any
    		steps
		{
                    withCredentials([string(credentialsId: 'CK_snykKey', variable: 'snyktoken')]) 
                       {
	                     catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')  
                               {
                        		bat 'C:\\Users\\Administrator\\Downloads\\snyk-win.exe  auth ' +snyktoken 
					bat 'C:\\Users\\Administrator\\Downloads\\snyk-win.exe container test alpine:latest'
                        		bat 'exit 0'
		                }
        		}
    		}
	}
stage('Checkov ->Checkout'){
        agent any
        steps{
            script{
            deleteDir()
           bat "git clone https://github.com/tooladi/adi_repo_code.git"
           stash "adi_repo_code"
            }
        }
        
    }
    stage('Checkov scan') {
        agent{
             docker {
                image 'kennethreitz/pipenv:latest'
                args '-u root --privileged -v /var/run/docker.sock:/var/run/docker.sock'
                label 'linagent2'
                         }
               }
        options { skipDefaultCheckout() }      
        steps {
            script {
                  unstash "adi_repo_code"
                    sh "ls -al"
                    dir('adi_repo_code'){
                    sh "ls -al"
                    sh "pipenv install"
                    sh "pipenv run pip install checkov"
                    sh "pipenv run checkov --directory terragoat-master/terraform/ --quiet --download-external-modules true --framework terraform -o json > Checkov.json || true"
                    def report_name='Checkov.json'
                    stash allowEmpty: true, includes: report_name, name: report_name 
                   }
                 }  
              }
          }
	stage('defect-dojo')
		{
			agent any
			steps
			{
				script
					{
		                                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')  {
						def report_name='Checkov.json'
			                       //stash allowEmpty: true, includes: report_name, name: 'Checkov.json'
			                       ws('C:\\Users\\Administrator\\defectdojo_api\\examples\\v2')
						{
							unstash report_name
							bat 'python dojo_ci_cd.py --product=2 --file '+report_name+' --scanner="Checkov Scan" --high=0 --host=https://demo.defectdojo.org --api_token=DefectDojoApiTokenKey --user=admin --engagement=1 --active TRUE'
			                         }
						bat 'exit 0'
					}
				}
			}
		}
	}
}