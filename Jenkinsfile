pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Clona el repositorio de tu proyecto
                git branch: 'main', url: 'https://github.com/TomasManriquez/TareaInfo279.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                // Construye tu proyecto (ejemplo: usando Maven o Gradle)
                sh 'pip install -r requirements.txt'
                sh 'python -m spacy download es_core_news_sm'
            }
        }
       stage('Run Notebook') {
            steps {
                sh 'jupyter nbconvert --to script Tarea.ipynb --output Tarea.py'
                sh 'python Tarea.py'
            }
        }
        stage('SonarQube Analysis') {
            enironment{
                scannerHome = tool 'SonaQube Scanner'
            }
            steps {
                // Ejecuta el análisis de SonarQube
                withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner \
                    -Dsonar.proyectKey=my_python_proyect \
                    -Dsonar.sources=. \
                    -Dsonar.language=py \
                    -Dsonar.sourceEncoding=UTF-8"
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
