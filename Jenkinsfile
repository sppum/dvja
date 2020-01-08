pipeline {
  agent any

  tools {
    maven "apache-maven-3.6.3"
  }

  stages {
    stage('Style check') {
        steps {
            sh "wget https://github.com/checkstyle/checkstyle/releases/download/checkstyle-8.28/checkstyle-8.28-all.jar"
            sh "wget https://raw.githubusercontent.com/checkstyle/checkstyle/master/src/main/resources/sun_checks.xml"
            sh "wget https://raw.githubusercontent.com/checkstyle/checkstyle/master/src/main/resources/google_checks.xml"
            sh "chown jenkins:jenkins checkstyle*.jar *_checks.xml"
            sh "java -jar checkstyle-8.28-all.jar -c /sun_checks.xml src/*.java"
            sh "java -jar java -jar checkstyle-8.28-all.jar -c /google_checks.xml src/*.java"
        }
    }
    stage('Build') {
      steps {
        git 'https://github.com/ajlanghorn/dvja.git'
        sh "mvn clean package"
      }
    }
    stage('Check dependencies') {
      steps {
        dependencyCheck additionalArguments: '', odcInstallation: 'Dependency-Check'
        dependencyCheckPublisher pattern: ''
      }
    }
    stage('Publish to S3') {
      steps {
        sh "aws s3 cp /var/lib/jenkins/workspace/dvja/target/dvja-1.0-SNAPSHOT.war s3://jenkins-buildartifacts-twss5hj4ay2o/dvja-1.0-SNAPSHOT.war"
      }
    }
    stage('Tidy up') {
      steps {
        cleanWs()
      }
    }
  }
}
