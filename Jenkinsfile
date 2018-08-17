pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        fileExists 'composer.phar'
        sh 'php -v'
      }
    }
  }
}