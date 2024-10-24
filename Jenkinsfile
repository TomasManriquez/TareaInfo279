pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Clona el repositorio de tu proyecto
                git branch: 'main', url: 'https://github.com/TomasManriquez/TareaInfo279.git'
            }
        }
        stage('Install Python') {
            steps {
                sh '''
                # Verificar si Python está instalado
                if ! command -v python3 &> /dev/null
                then
                    echo "Python3 no está instalado. Instalando Python3 y pip..."
                    apt-get update && apt-get install -y python3 python3-pip
                fi
                
                # Verificar si pip está disponible
                if ! command -v pip3 &> /dev/null
                then
                    echo "pip3 no está instalado. Instalando pip3..."
                    apt-get install -y python3-pip
                fi
                '''
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
            environment{
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
