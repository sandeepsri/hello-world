pipeline {
  agent any
  stages {
    stage ('Checkout') {
      steps {
        git 'https://github.com/sandeepsri/hello-world.git' 
      }
    }
    stage ('check-git-secrets') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/sandeepsri/hello-world.git > trufflehog'
      }
    }
    stage ('SAST') {
      environment {
          scannerHome = tool 'sonnar'
      }
      steps {
        withSonarQubeEnv('sonarqube') {
         // sh 'mvn clean package sonar:sonar'
          sh "${scannerHome}/bin/sonar-scanner"
         // sh 'cat target/sonar/report-task.txt'
        }
      }
    }
    stage ('Source-Composition-Analysis') {
      steps {
        sh 'rm owasp* || true'
        sh 'wget https://github.com/sandeepsri/hello-world/master/owasp-dependency-check.sh'
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'bash owasp-dependency-check.sh'
      }
    }
   stage ('Build') {
     steps {
        sh 'mvn clean package'
      }
    }
    stage ('Deploy-to-tomcat') {
      steps {
        sh 'cp target/*.war ~/tomcat9/webapps/'
      }
    }
    stage ('DAST') {
      steps {
        sh 'docker run owasp/zap2docker-stable zap-baseline.py -t http://localhost:8083/hello-world'
      }
    }
  }
}
