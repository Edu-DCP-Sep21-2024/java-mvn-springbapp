pipeline {
    agent { label 'slave1' }
	
	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	}

    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM_Checkout'
				git 'https://github.com/Edu-DCP-Sep21-2024/java-mvn-springbapp.git'
            }
        }
        stage('Application_Build') {
            steps {
                echo 'Perform Application Build'
				sh 'mvn clean package'
				
            }
        }
        stage('Build Docker Image') {
            steps {
				sh 'docker version'
				sh "docker build -t loksaieta/dcp-sep21-webapp:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag loksaieta/dcp-sep21-webapp:${BUILD_NUMBER} loksaieta/dcp-sep21-webapp:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "docker push loksaieta/dcp-sep21-webapp:latest"
			}
		}
        stage('Deploy to Kubernetes') {
            steps {
                script{
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'Kubernetes-Master', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubedeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }		
    }
}
