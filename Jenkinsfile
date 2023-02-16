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
					git credentialsId: 'poncho_curso_devops',
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
		}
}

