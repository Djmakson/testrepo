pipeline {
    agent any

    stages {
        stage('Git Clone') {
            steps {
                 git branch: 'main', url: 'https://github.com/Djmakson/testrepo'
            }
        }
        stage("SonarQube analysis") {
           steps {
             withSonarQubeEnv('sonarqube') {
                 sh 'mvn clean package sonar:sonar'
             }
           }
        }
        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true, credentialsId: 'sonarqube'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Deploy to Nexus') {
            steps {
               nexusArtifactUploader artifacts: [[artifactId: 'RegistrationApp',
               classifier: '', file: 'target/RegistrationApp-1.3.war',
               type: 'war']], 
               credentialsId: 'nexus', 
               groupId: 'com.example', 
               nexusUrl: '44.202.46.89:8081', 
               nexusVersion: 'nexus3', 
               protocol: 'http', 
               repository: 'my-repo1',
               version: '1.4'
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'mytomcat', path: '', url: 'http://34.239.104.232:8080')], contextPath: 'reg', war: 'target/RegistrationApp-1.2.war'
            }
        }
    }
}
