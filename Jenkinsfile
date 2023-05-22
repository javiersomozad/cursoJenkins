#!groovy

pipeline {
	agent {
		node {
			label 'master'
		}
	}
	options {
		timestamps()
	}
	stages {
		stage('Limpiar WorkSpace') {
			steps {
				cleanWs()
			}
		}
		stage('Prepare Environment') {
			steps {
				git branch: '$BRANCH_NAME', url: 'git@github.com:javiersomozad/cursoJenkins.git'
			}
		}
		stage('Compile Project') {
			steps {
				sh 'mvn compile -f jenkins-maven-project/pom.xml'
			}
		}
		stage('Unit Testing') {
			steps {
				sh 'mvn test -f jenkins-maven-project/pom.xml'
			}
		}
		stage('Package') {
			steps {
				sh 'mvn package -f jenkins-maven-project/pom.xml'
			}
		}
		stage("Publicacion en el reposotiro de nexus") {
            steps {
          		nexusArtifactUploader {
            	nexusVersion('nexus3')
            protocol('http')
            nexusUrl('localhost:8071/miRepo')
            groupId('sp.sd')
            version('2.4')
            repository('NexusArtifactUploader')
            credentialsId('credencialNexus')
            artifact {
                artifactId('nexus-artifact-uploader')
                type('jar')
                classifier('debug')
                file('nexus-artifact-uploader.jar')
            }
            artifact {
                artifactId('nexus-artifact-uploader')
                type('hpi')
                classifier('debug')
                file('nexus-artifact-uploader.hpi')
            }
          }
        }
        }
	}
}