pipeline {
  agent any
  tools {
    maven 'M3'
    jdk 'java11'
  }
  stages {
    stage ('Initialize') {
      steps {
          sh '''
              echo "PATH = ${PATH}"
              echo "M2_HOME = ${M2_HOME}"
            '''
      }
    }
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
    stage ('Build') {
     steps {
        sh 'mvn clean package'
      }
    }
    stage ('SAST') {     
      steps {
        withSonarQubeEnv('sonarqube') {
          sh 'mvn sonar:sonar'
         // sh "${scannerHome}/bin/sonar-scanner"
         // sh 'cat target/sonar/report-task.txt'
          //sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:4.5.0.2216:sonar'
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
