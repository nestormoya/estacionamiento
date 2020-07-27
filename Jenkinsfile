pipeline {
  //Donde se va a ejecutar el Pipeline
  agent {
    label 'Slave_Mac'
  }

  //Opciones específicas de Pipeline dentro del Pipeline
  // options {
    // buildDiscarder(logRotator(numToKeepStr: '3'))
      // disableConcurrentBuilds()
  // }

  //Una sección que define las herramientas “preinstaladas” en Jenkins
  // tools {
    // jdk 'JDK8_Centos' //Preinstalada en la Configuración del Master
    // gradle 'Gradle4.5_Centos' //Preinstalada en la Configuración del Master
  // }

  //Aquí comienzan los “items” del Pipeline
  stages{
    stage('Checkout') {
      steps{
        echo "------------>Checkout<------------"
        checkout([
            $class: 'GitSCM',
            branches: [[name: 'master']],
            doGenerateSubmoduleConfigurations: false,
            extensions: [], 
            submoduleCfg: [],
            userRemoteConfigs: [[
                credentialsId: 'GitHub_nestormoya',
                url: 'https://github.com/nestormoya/estacionamiento.git'
            ]]
        ])
      }
    }
    
    stage('Compile & Unit Tests') {
      steps{
        echo "------------>Unit Tests<------------"

      }
    }

    stage('Static Code Analysis') {
      steps{
        echo '------------>Análisis de código estático<------------'
        withSonarQubeEnv('Sonar') {
        //  sh "${tool name: 'SonarScanner', type:'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner"
        }
      }
    }

    stage('Build') {
      steps {
        echo "------------>Build<------------"
        // sh 'xcodebuild -scheme "estacionamiento" -configuration "Debug" build test -destination "platform:iOS Simulator, OS:latest, name:iPhone 8"'
	sh "xcodeBuild allowFailingBuildResults: true, cleanResultBundlePath: false, configuration: 'Release', ipaExportMethod: 'ad-hoc', keychainPwd: <object of type hudson.util.Secret>, xcodeName: 'Xcode_Mac', xcodeProjectFile: 'estacionamiento.xcodeproj', xcodeProjectPath: '', xcodeSchema: 'estacionamiento'"
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
      mail (to: 'nestor.moy@ceiba.com.co',subject: "Failed Pipeline:${currentBuild.fullDisplayName}",body: "Something is wrong with ${env.BUILD_URL}")
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
