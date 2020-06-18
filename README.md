# Pipeline_Script

https://github.com/r9kumar/jenkins-pipeline-script
https://www.jenkins.io/doc/book/pipeline/jenkinsfile/
https://www.jenkins.io/doc/pipeline/examples/
https://www.eficode.com/blog/jenkins-groovy-tutorial
https://www.youtube.com/watch?v=FhDomw6BaHU

use following command as one build job:
echo "First Job : Triggered at %date% and %time%

#############################
# GLOBAL TOOL CONFIGURATION #
#############################

Maven Configuration

JDK

Git

JENKINS GIT
You have to set the path to git.exe in Jenkins:
	1. You have to go to http://yourjenkinsserver:8080/configureTools/
	2. There should be a field next to Path to Git executable.
	3. Put in that field the path to your C:\Program Files\Git\mingw64\bin\git.exe.
	

JENKINS AGENT	
https://www.jenkins.io/projects/remoting/

	1. how to bring/add up agent node via the Jenkins UI?
	   possible errors - 
	   a) you have to install java for this ( i.e., to run .jnlp file and other java related commands).
	   b) JNLP agent port is disabled and agents cannot connect this way.
	   https://www.edureka.co/community/70625/disabled-agents-cannot-connect-security-configuration-screen
	   c) Error: Unable to access jarfile agent.jar
	   https://www.edureka.co/community/70626/error-unable-to-access-jarfile-agent-jar
	   You have to manually add agent.jar file in that folder from where cmd is launched.
	   Else in absence of agent.jar, you can connect to slave through .jnlp file (Launch from WEB/UI)

	2. how to bring/add up remote agent node via the Jenkins UI?
	3. how to start agents via the Jenkins UI or CLI?
	   possible errors:
	   a)Connection was broken. java.nio.channels.ClosedChannelException
	4. where to find all agents related stuffs (like status, configuration, etc) in jenkins?
	5. where is agent's workspace in jenkins?
	
https://wiki.jenkins.io/display/JENKINS/Step+by+step+guide+to+set+up+master+and+agent+machines+on+Windows#:~:text=Open%20a%20browser%20on%20the,you%20have%20configured%20global%20security.
https://linuxacademy.com/blog/linux-academy/adding-a-jenkins-agent-node/#:~:text=In%20Jenkins%2C%20go%20to%20Manage,now%20is%20the%20Launch%20Method.

####################
# TRIVIAL PIPELINE #
####################
1. using New View -> Delivery Pipeline View (using Delivery Pipeline from jenkins marketplace)
2. using New View -> Delivery Pipeline View for Jenkins Pipelines (pre-installed plugins)

#################################################
# Jenkinsfile - Scripted + Declarative Pipeline #
#################################################
pipeline {
  agent any
  stages {
	stage('Build') {
	    steps {
		sh 'make' 
		archiveArtifacts artifacts: '../target/*.jar', fingerprint: true 
	    }
	    steps {
		echo 'Building...'
		echo 'compiled successfully'
	    }
	}	
	stage('Test') {
	    steps {
		sh 'make check || true' 
		junit '../target/*.xml' 
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


#################################
# scripted_pipeline_simple_demo #
#################################
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
				echo 'compiled successfully'
            }
        }
		
        stage('Test') {
            steps {
                echo 'Testing...'
				echo 'unit tests and integration tests passed successfully'
            }
        }
	
        stage('Deploy') {
            steps {
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

############################
# scripted_pipeline_github #
############################
pipeline {
    agent any

    stages {
	
		stage('Git Checkout') {
            steps {
                echo 'Checking out from Git Repo ...'
				git credentialsId: '0fd57e51-5b5d-44ef-9038-e940d7ef9014', url: 'https://github.com/r9kumar/jenkins-pipeline-script'
				echo 'checking out successfully'
            }
        }
		
        stage('Build') {
            steps {
                echo 'Building...'
				bat 'Build.bat'
				echo 'compiled successfully'
            }
        }
		
        stage('Unit-Test') {
            steps {
                echo 'Running JUnit Tests...'
				bat 'Unit.bat'
				echo 'unit tests completed successfully'
            }
        }
		
		stage('Quality-Gate') {
            steps {
                echo 'Running JUnit Tests...'
				bat 'Quality.bat'
            }
        }
	
        stage('Deploy') {
            steps {
				echo 'Deploying...'
				bat 'Deploy.bat'
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

##############################
# scripted_pipeline_on_agent #
##############################
pipeline {
    agent {
		label 'window-agent-1'
	}

    stages {
	
		stage('Git Checkout') {
            steps {
                echo 'Checking out from Git Repo ...'
				git credentialsId: '0fd57e51-5b5d-44ef-9038-e940d7ef9014', url: 'https://github.com/r9kumar/jenkins-pipeline-script'
				echo 'checking out successfully'
            }
        }
		
        stage('Build') {
            steps {
                echo 'Building...'
				bat 'Build.bat'
				echo 'compiled successfully'
            }
        }
		
        stage('Unit-Test') {
            steps {
                echo 'Running JUnit Tests...'
				bat 'Unit.bat'
				echo 'unit tests completed successfully'
            }
        }
		
		stage('Quality-Gate') {
            steps {
                echo 'Running JUnit Tests...'
				bat 'Quality.bat'
            }
        }
	
        stage('Deploy') {
            steps {
				echo 'Deploying...'
				bat 'Deploy.bat'
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

#######################################
# scripted_pipeline_on_parallel_agent #
#######################################
pipeline {
    agent none

    stages {
	
		stage('Non-Parallel Stage') {
			agent {
				label "master"
			}
            steps {
                echo 'This stage will be executed first ...'
            }
        }
		
        stage('Run Tests') {
			parallel {
				stage('Test On Windows') {
					agent {
						label "window-agent-1"
					}
					steps {
						echo 'Task1 on Agent...'
					}
				}
				stage('Test On Master') {
					agent {
						label "master"
					}
					steps {
						echo 'Task1 on Master...'
					}
				}
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

######################################
# scripted_pipeline_user_interaction #
######################################
pipeline {
    agent any

    stages {
	
		stage('Git Checkout') {
            steps {
                echo 'Checking out from Git Repo ...'
				git credentialsId: '0fd57e51-5b5d-44ef-9038-e940d7ef9014', url: 'https://github.com/r9kumar/jenkins-pipeline-script'
				echo 'checking out successfully'
            }
        }
		
		stage('User Interaction') {
            steps {
                input('Do you want to proceed?')
            }
        }
		
        stage('Build') {
            steps {
                echo 'Building...'
				bat 'Build.bat'
				echo 'compiled successfully'
            }
        }
		
        stage('Unit-Test') {
            steps {
                echo 'Running JUnit Tests...'
				bat 'Unit.bat'
				echo 'unit tests completed successfully'
            }
        }
		
		stage('Quality-Gate') {
            steps {
                echo 'Running JUnit Tests...'
				bat 'Quality.bat'
            }
        }
	
        stage('Deploy') {
            steps {
				echo 'Deploying...'
				bat 'Deploy.bat'
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

##############
# Blue Ocean #
##############

......
