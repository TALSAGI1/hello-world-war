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

        stage('Set up JDK 17') {
            steps {
                script {
                    env.JAVA_HOME = tool name: 'JDK 17', type: 'hudson.model.JDK'
                    env.PATH = "${env.JAVA_HOME}\\bin;${env.PATH}"
                }
            }
        }

        stage('Set up Maven') {
            steps {
                script {
                    env.MAVEN_HOME = tool name: 'Maven', type: 'hudson.tasks.Maven$MavenInstallation'
                    env.PATH = "${env.MAVEN_HOME}\\bin;${env.PATH}"
                }
            }
        }

        stage('Build with Maven') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Run SonarQube Analysis') {
            steps {
               withSonarQubeEnv( installationName: 'sonar') {
                    bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar -Dsonar.projectKey=TALSAGI1_hello-world-war -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=%SONAR_TOKEN%'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t hello-world-war:%BUILD_ID% -f module4/Dockerfile .'
            }
        }

        stage('Tag and Push Docker Image') {
            steps {
                script {
                    bat 'docker tag hello-world-war:%BUILD_ID% <your-docker-repo>/hello-world-war:%BUILD_ID%'
                    bat 'echo %DOCKER_CREDENTIALS_PSW% | docker login -u %DOCKER_CREDENTIALS_USR% --password-stdin'
                    bat 'docker push <your-docker-repo>/hello-world-war:%BUILD_ID%'
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
