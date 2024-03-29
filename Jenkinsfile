pipeline {
  //Donde se va a ejecutar el Pipeline
  agent {
    label 'Slave_Mac'
  }

  //Opciones específicas de Pipeline dentro del Pipeline
  options {
    buildDiscarder(logRotator(numToKeepStr: '3'))
    disableConcurrentBuilds()
  }

  //Aquí comienzan los “items” del Pipeline
  stages{
    stage('Build') {
      steps {
        echo "------------>Build<------------"
	      sh 'xcodebuild clean build CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO CODE_SIGN_ENTITLEMENTS="" CODE_SIGNING_ALLOWED="NO"'
      }
    }  
    stage('Unit Tests') {
      steps{
        echo "------------>Unit Tests<------------"
        sh 'xcodebuild -scheme "estacionamiento" -configuration "Debug" test -destination "platform=iOS Simulator,name=iPhone 11,OS=14.5" -enableCodeCoverage YES'
      }
    }
    stage('Static Code Analysis') {
      tools {
        jdk 'JDK11_Mac'
      }  
      steps{
        echo '------------>Análisis de código estático<------------'
        sh 'swiftlint lint > swiftlint.txt || true'
        withSonarQubeEnv('Sonar') {
            sh "${tool name: 'SonarScanner', type:'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner -Dsonar.swift.swiftlint.report=swiftlint.txt -Dproject.settings=sonar-project.properties"
        }
      }
    }
  }

  post {
    always {
      echo 'This will always run'
    }
    success {
			echo 'This will run only if successful'
      // junit 'build/test-results/test/*.xml' → RUTA DE TUS ARCHIVOS .XML
    }
    failure {
      echo 'This will run only if failed'
      mail (to: 'nestor.moya@ceiba.com.co',subject: "Failed Pipeline:${currentBuild.fullDisplayName}",body: "Something is wrong with ${env.BUILD_URL}")
    }
    unstable {
      echo 'This will run only if the run was marked as unstable'
    }
    changed {
      echo 'This will run only if the state of the Pipeline has changed'
      echo 'For example, if the Pipeline was previously failing but is now successful'
    }
  }
}
