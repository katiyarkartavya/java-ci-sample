pipeline {
  agent any

  tools {
    jdk 'jdk17'          // matches Tools config
    maven 'maven-3.9'    // matches Tools config
  }

  options {
    skipDefaultCheckout(true)     // we’ll checkout explicitly
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '20'))
  }

  triggers {
    // Optional: if you configure a GitHub webhook later
    // githubPush()
  }

  stages {
    stage('Checkout') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/main']],
          userRemoteConfigs: [[
            url: 'https://github.com/<your-username>/java-ci-sample.git',
            
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
