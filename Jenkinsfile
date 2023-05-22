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
          		nexusArtifactUploader(
        			nexusVersion: 'nexus3',
        			protocol: 'http',
        			nexusUrl: 'http://localhost:8071/miRepo',
        			groupId: 'com.example',
        			version: version,
        			repository: 'miRepo',
        			credentialsId: 'credencialNexus',
        			artifacts: [
            			[artifactId: projectName,
             				classifier: '',
             				file: 'my-service-' + version + '.jar',
             				type: 'jar'
						]
        			]
     			)
        	}
        }
	}
}