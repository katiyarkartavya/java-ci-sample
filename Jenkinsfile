pipeline {
  agent any

  tools {
    jdk 'JDK21'         
    maven 'Maven_home'    
  }

  options {
    skipDefaultCheckout(true)
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
            url: 'https://github.com/katiyarkartavya/java-ci-sample.git'
          ]]
        ])
      }
    }

    stage('Build') {
      steps {
        script {
          if (isUnix()) {
            sh 'mvn -v'
            sh 'mvn -B -ntp clean compile'
          } else {
            bat 'mvn -v'
            bat 'mvn -B -ntp clean compile'
          }
        }
      }
    }

    stage('Test') {
      steps {
        script {
          if (isUnix()) {
            sh 'mvn -B -ntp test'
          } else {
            bat 'mvn -B -ntp test'
          }
        }
      }
      post {
        always {
          junit '**/target/surefire-reports/*.xml'
        }
      }
    }

    stage('Package') {
      steps {
        script {
          if (isUnix()) {
            sh 'mvn -B -ntp package'
          } else {
            bat 'mvn -B -ntp package'
          }
        }
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }
  }

  post {
    success { echo 'Build & tests succeeded 🎉' }
    failure { echo 'Build or tests failed. Check logs and JUnit report.' }
  }
}
