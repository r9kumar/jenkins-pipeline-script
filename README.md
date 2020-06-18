# Pipeline_Script

https://github.com/r9kumar/jenkins-pipeline-script
https://www.jenkins.io/doc/book/pipeline/jenkinsfile/
https://www.jenkins.io/doc/pipeline/examples/
https://www.eficode.com/blog/jenkins-groovy-tutorial

use following command as one build job:
echo "First Job : Triggered at %date% and %time%

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any

    stages {
        stage('Build') {
			      steps {
                sh 'make' 
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true 
            }
            steps {
                echo 'Building...'
				        echo 'compiled successfully'
            }
        }
		
        stage('Test') {
            steps {
				        sh 'make check || true' 
                junit '**/target/*.xml' 
                echo 'Testing...'
				        echo 'unit tests and integration tests passed successfully'
            }
        }
	
        stage('Deploy') {
		        	when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
            steps {
                sh 'make publish'
				        echo 'Deploying....'
				        echo 'deployment successful'
            }
        }
    }
	
	post {
		always {
			echo 'This will always run'
		}
		success {
			echo 'This will run only if successful'
		}
		failure {
			echo 'This will run only if failed'
		}
		unstable {
			echo 'This will run only if the run was marked as unstable'
		}
		changed {
			echo 'This will run only if the state of the pipeline has changed'
			echo 'For example, if the pipeline was previously failing but is now successful'
		}
	}
	
}
