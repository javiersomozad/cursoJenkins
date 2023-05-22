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
	environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "localhost:8071"
        NEXUS_REPOSITORY = "repo"
        NEXUS_CREDENTIAL_ID = "credencialNexus"
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
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
	}
}