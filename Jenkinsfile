pipeline {
  agent {
    node {
      label 'test'
    }

  }
  stages {
    stage('compile') {
      steps {
        sh './mvnw clean compile'
      }
    }

    stage('static analysis') {
      steps {
        sh '''./mvnw clean verify sonar:sonar \\
  -Dsonar.projectKey=petclinic \\
  -Dsonar.projectName=\'petclinic\' \\
  -Dsonar.host.url=http://172.31.24.5:9000 \\
  -Dsonar.token=sqp_5575d07169c12320283507a02bf45d8fc22d22a5'''
      }
    }

    stage('unit test') {
      steps {
        sh './mvnw "-Dtest=**/petclinic/*/*.java" test'
      }
    }

    stage('package') {
      steps {
        sh './mvnw package -DskipTests=true'
      }
    }

    stage('deploy') {
      parallel {
        stage('deploy') {
          steps {
            sh './mvnw spring-boot:run </dev/null &>/dev/null &'
          }
        }

        stage('Integration and performance tests') {
          steps {
            sh './mvnw verify'
            junit '**/target/surefire-reports/'
            perfReport(sourceDataFiles: ' **/target/jmeter/results/*')
          }
        }

      }
    }

  }
}