pipeline {
      agent any
        stages {
            stage ('Build') {
              steps {
                git 'https://github.com/sandeepsri/hello-world.git'
              }
            }
            stage ('check-git-secrets') {
              steps {
                sh 'rm trufflehog || true'
                sh 'docker run -t gesellix/trufflehog --json https://github.com/sandeepsri/hello-world.git > trufflehog'
              }
            }
            stage ('Source-Composition-Analysis') {
              steps {
                sh 'rm owasp*'
                sh 'wget https://github.com/sandeepsri/hello-world/master/owasp-dependency-check.sh'
                sh 'chmod +x owasp-dependency-check.sh'
                sh 'bash owasp-dependency-check.sh'
              }
            }
            stage ('SAST') {
              steps {
                withSonarQubeEnv('sonar') {
                  sh 'mvn sonar:sonar'
                  sh 'cat target/sonar/report-task.txt'
                }
              }
            } 
          stage ('DAST') {
              steps {
                sshagent(['ZAP-SSH']) {
                  sh 'run -t owasp/zap2docker-stable zap-baseline.py -t http://localhost:8080/hello-world'
                }
              }
            }
        }
}
