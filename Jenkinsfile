pipeline {
  agent any

  tools {
    maven "apache-maven-3.6.3"
  }

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/ajlanghorn/dvja.git'
        sh "mvn clean package"
        recordIssues enabledForFailure: true, tool: mavenConsole(), referenceJobName: 'Plugins/warnings-ng-plugin/master'
        recordIssues enabledForFailure: true, tools: [java(), javaDoc()], sourceCodeEncoding: 'UTF-8', referenceJobName: 'Plugins/warnings-ng-plugin/master'
        recordIssues enabledForFailure: true, tool: checkStyle(pattern: 'target/checkstyle-result.xml'), sourceCodeEncoding: 'UTF-8', referenceJobName: 'Plugins/warnings-ng-plugin/master'
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
