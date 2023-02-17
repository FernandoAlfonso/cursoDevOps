// Jenkinsfile para comunicar jenkins con git
pipeline {
	environment {
		dockerImageName = "fernandofar/app:v1"
		dockerImage1 = ""
	}
	
	agent any
		stages {
			stage ('Revisar código') {
				steps {
					git credentialsId: 'poncho_curso_devops', // nombre de variable por default
					url: 'https://github.com/FernandoAlfonso/cursoDevOps.git',
					branch: 'main'
				}
			}

			stage ('Construir la imagen de la aplicación') {
				steps {
					dir ('app') {
						script {
							dockerImage1 = docker.build dockerImageName
						}
					}
				}
			}

			stage ('Subir la imagen App') {
				environment {
					registryCredential = 'poncho_curso_docker_token' // nombre de variable por default
				}

				steps {
					dir ('app') {
						docker.withRegistry ('https://registry.hub.docker.com', registryCredential) {
							dockerImage1.push ('v1') // tag
						}
					}
				}
			}
		}
}

