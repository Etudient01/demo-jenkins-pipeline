pipeline {
  agent {
    docker {
      // Image officielle Maven avec JDK 17 (Temurin)
      image 'maven:3.9.9-eclipse-temurin-17'
      // Monte le cache Maven hôte vers le conteneur pour accélérer les builds
      // (si Jenkins tourne sur le même hôte Docker)
      args '-v $HOME/.m2:/root/.m2'
    }
  }

  options {
    // Affiche en live chaque commande shell
    ansiColor('xterm')
    timestamps()
  }

  environment {
    // Evite les téléchargements de logs verbeux Maven
    MAVEN_OPTS = '-Dmaven.wagon.http.pool=false'
  }

  stages {

    stage('Checkout') {
      steps {
        // Récupère le code de ton dépôt (configuré via "Pipeline script from SCM")
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh 'mvn -B -ntp -q clean compile'
      }
    }

    stage('Test') {
      steps {
        sh 'mvn -B -ntp test'
      }
      post {
        always {
          // Publie les rapports JUnit pour l’onglet "Test Result"
          junit allowEmptyResults: false, testResults: 'target/surefire-reports/*.xml'
          // Archive (optionnel) les rapports et le jar pour consultation
          archiveArtifacts artifacts: 'target/**/*.xml, target/*.jar', fingerprint: true
        }
      }
    }

    stage('Package') {
      steps {
        sh 'mvn -B -ntp package -DskipTests'
      }
    }
  }

  post {
    success {
      echo '✅ Pipeline OK : build + tests passés !'
    }
    unstable {
      echo '🟠 Pipeline instable (tests ignorés/instables ?)'
    }
    failure {
      echo '❌ Pipeline en échec (voir console + rapports de tests).'
    }
    always {
      echo "Fin de pipeline – branche: ${env.BRANCH_NAME}, build #${env.BUILD_NUMBER}"
    }
  }
}