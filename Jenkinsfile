pipeline {
  agent any

  tools {
    maven "apache-maven-3.6.3"
  }

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/sppum/dvja.git'
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
  post {
    always {
      def mvnHome = tool 'mvn-default'

      sh "${mvnHome}/bin/mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs"

      def checkstyle = scanForIssues tool: checkStyle(pattern: '**/target/checkstyle-result.xml')
      publishIssues issues: [checkstyle]

      def pmd = scanForIssues tool: pmdParser(pattern: '**/target/pmd.xml')
      publishIssues issues: [pmd]
      
      def cpd = scanForIssues tool: cpd(pattern: '**/target/cpd.xml')
      publishIssues issues: [cpd]
      
      def spotbugs = scanForIssues tool: spotBugs(pattern: '**/target/findbugsXml.xml')
      publishIssues issues: [spotbugs]

      def maven = scanForIssues tool: mavenConsole()
      publishIssues issues: [maven]
      
      publishIssues id: 'analysis', name: 'All Issues', 
          issues: [checkstyle, pmd, spotbugs], 
          filters: [includePackage('io.jenkins.plugins.analysis.*')]
    }
  }
}
