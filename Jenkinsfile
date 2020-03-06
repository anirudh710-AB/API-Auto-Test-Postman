pipeline {
    agent {
		dockerfile {
			args '--entrypoint=\'\''
		}
    }
	environment { 
        TEST_ENV = 'Local'
		TEST_DOMAIN = 'ab-optimus.local'
    }
    stages {
		
		stage('Run Postman') { 
			steps {
				sh "newman run ./DemoCollection.postman_collection.json -d TestData.json --reporters cli,junit --reporter-junit-export './reports/testReport.xml'"
				sh "find ./reports -type f -name testReport.xml -exec sed -i 's/classname=\"/classname=\"POSTMAN_ALL./g' {} \\; "
			}
			
            post {
                always {
                    junit 'reports/*.xml'
                }
            }
		}
		stage('Send email') {
			steps {
				slackSend (color: '#009900', message: "Jenkins Item Completed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) - Sending Notices")
				script {
					def mailRecipients = 'anirudh@agencybloc.com'
					def jobName = currentBuild.fullDisplayName
					emailext body: '''${SCRIPT, template="groovy-html.template"}''',
					mimeType: 'text/html',
					subject: "[Jenkins] ${jobName}",
					to: "${mailRecipients}",
					replyTo: "${mailRecipients}",
					recipientProviders: [[$class: 'CulpritsRecipientProvider']]
				}
			}
		}
	}
}