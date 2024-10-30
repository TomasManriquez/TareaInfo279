pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Clona el repositorio de tu proyecto
                git branch: 'main', url: 'https://github.com/TomasManriquez/TareaInfo279.git'
            }
        }
         stage('Build Docker Image') {
            steps {
                script {
                    // Define el nombre de la imagen y la ruta del Dockerfile
                    def imageName = 'my-python-app'
                    
                    // Crea un Dockerfile dinámicamente
                    writeFile file: 'Dockerfile', text: '''
                    FROM python:3.9-slim
    
                    # Establece el directorio de trabajo
                    WORKDIR /app
    
                    # Copia los archivos de requisitos
                    COPY requirements.txt ./
    
                    # Instala las dependencias
                    RUN pip install --no-cache-dir -r requirements.txt
                    '''
    
                    // Construye la imagen Docker
                    sh "docker build -t ${imageName} ."
    
                    // Limpia el Dockerfile después de la construcción
                    sh 'rm Dockerfile'
                }
            }
        
        stage('Install Python') {
            
            steps {
                sh '''
             
                # Verificar si Python está instalado
                if ! command -v python3 &> /dev/null
                then
                    echo "Python3 no está instalado. Instalando Python3 y pip..."
                    sudo apt-get update && apt-get install -y python3 python3-pip
                fi
                
                # Verificar si pip está disponible
                if ! command -v pip3 &> /dev/null
                then
                    echo "pip3 no está instalado. Instalando pip3..."
                    sudo apt-get install -y python3-pip
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
                script {
                    def imageName = 'my-python-app'
                    // Ejecuta el comando dentro del contenedor
                    sh "docker run --rm -v \${WORKSPACE}:/app ${imageName} jupyter nbconvert --to script Tarea.ipynb --output Tarea.py"
                    sh "docker run --rm -v \${WORKSPACE}:/app ${imageName} python Tarea.py"
                }
            }
        }
        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'SonarQube Scanner'
            }
            steps {
                script {
                    def imageName = 'my-python-app'
                    // Ejecuta el análisis de SonarQube dentro del contenedor
                    withSonarQubeEnv('SonarQube') {
                        sh "docker run --rm -v \${WORKSPACE}:/app ${imageName} ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=my_python_project \
                        -Dsonar.sources=. \
                        -Dsonar.language=py \
                        -Dsonar.sourceEncoding=UTF-8"
                    }
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
