pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        DOCKER_CREDENTIALS = credentials('docker-credentials-id')
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Set up JDK 11.0.21') {
            steps {
                script {
                    env.JAVA_HOME = tool name: 'JDK 11', type: 'hudson.model.JDK'
                    env.PATH = "${env.JAVA_HOME}/bin:${env.PATH}"
                }
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Run SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=your_project_key -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${SONAR_TOKEN}'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t hello-world-war:${BUILD_ID} -f module4/Dockerfile .'
            }
        }

        stage('Tag and Push Docker Image') {
            steps {
                script {
                    sh 'docker tag hello-world-war:${BUILD_ID} <your-docker-repo>/hello-world-war:${BUILD_ID}'
                    sh 'echo ${DOCKER_CREDENTIALS_PSW} | docker login -u ${DOCKER_CREDENTIALS_USR} --password-stdin'
                    sh 'docker push <your-docker-repo>/hello-world-war:${BUILD_ID}'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
