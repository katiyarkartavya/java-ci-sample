pipeline {
  agent any

  tools {
    jdk 'JDK21'          // matches Tools config
    maven 'Maven_home'    // matches Tools config
  }

  options {
    skipDefaultCheckout(true)     // we’ll checkout explicitly
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '20'))
  }

  

  stages {
    stage('Checkout') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/main']],
          userRemoteConfigs: [[
            url: 'https://github.com/katiyarkartavya/java-ci-sample.git',
            
          ]]
        ])
      }
    }

    stage('Build') {
      steps {
        sh 'mvn -v'
        sh 'mvn -B -ntp clean compile'
      }
    }

    stage('Test') {
      steps {
        sh 'mvn -B -ntp test'
      }
      post {
        always {
          junit '**/target/surefire-reports/*.xml'
        }
      }
    }

    stage('Package') {
      steps {
        sh 'mvn -B -ntp package'
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }
  }

  post {
    success {
      echo 'Build & tests succeeded 🎉'
    }
    failure {
      echo 'Build or tests failed. Check logs and JUnit report.'
    }
  }
}
