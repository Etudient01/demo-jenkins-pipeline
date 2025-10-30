pipeline {
    agent any 

    tools {
        maven 'installation-auto'
    }

    stages {
        stage('Cloner le dépôt') {
            steps {
                echo 'Clonage du dépot'
                checkout scm
            }
        }
        stage('Compiler le projet') {
            steps {
                echo 'Compilation du projet'
                sh 'mvn clean compile'
            }
        }
        stage('Exécuter les tests') {
            steps {
                echo 'Exécution des tests'
                sh 'mvn test'
            }
        }
        stage('Publier les résultats') {
            steps {
                echo 'Publication des rapports de tests'
                junit '*/target/surefire-reports/.xml'
            }
        }
    }
    post {
        success {
            echo 'Build réussi !'
        }
        failure {
            echo 'Build échoué.'
        }
    }
}