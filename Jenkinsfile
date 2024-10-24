pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Clona el repositorio de tu proyecto
                git branch: 'main', url: 'https://github.com/TomasManriquez/TareaInfo279.git'
            }
        }
        stage('Build') {
            steps {
                // Construye tu proyecto (ejemplo: usando Maven o Gradle)
                sh './mvnw clean install'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                // Ejecuta el análisis de SonarQube
                withSonarQubeEnv('SonarQube Local') {
                    sh './mvnw sonar:sonar'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                // Espera a que SonarQube procese el análisis y falle el pipeline si no pasa la Quality Gate
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
