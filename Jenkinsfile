pipeline {
  agent any
//  tools {
//    maven 'Maven_3_8_7'
//  }

  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh("mvn -Dmaven.test.failure.ignore verify sonar:sonar -Dsonar.login=$SONAR_TOKEN -Dsonar.projectKey=vulnerablejava -Dsonar.host.url=http://localhost:9000/")
        }
      }
    }
//    stage('Build') {
//      steps {
//        withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
//          script {
//            app = docker.build("python:3.4-alpine")
//          }
//        }
//      }
//    }
    stage('RunContainerScan') {
      steps {
        script {
          withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
              try {
                sh("export SNYK_TOKEN=$SNYK_TOKEN")
                sh("/var/jenkins_home/snyk-linux container test python:3.4-alpine")
              } catch (err) {
                echo err.getMessage()
            }
          }
        }
      }
    }
    stage('RunSnykSCA') {
      steps {
        script {
          withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
              try {
                sh("export SNYK_TOKEN=$SNYK_TOKEN")
                sh("mvn snyk:test -fn")
              } catch (err) {
                echo err.getMessage()
            }
          }
        }
      }
    }
    stage('RunDASTUsingZAP') {
      steps {
//        bat("C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\Output.html")
        sh("/var/jenkins_home/ZAP_D-2025-05-05/zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout /var/jenkins_home/ZAP_D-2025-05-05/Output.html")
      }
    }

    stage('checkov') {
      steps {
        sh("/var/jenkins_home/.local/bin/checkov -s -f main.tf")
      }
    }
  }
}
