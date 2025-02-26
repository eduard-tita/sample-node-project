pipeline {
  agent any

  tools {
    nodejs 'node 16'
  }

  options {
    buildDiscarder logRotator(
        artifactDaysToKeepStr: '',
        artifactNumToKeepStr: '',
        daysToKeepStr: '1',
        numToKeepStr: '5'
    )
  }

  stages {

    stage('Build') {
      steps {
        script {
          sh 'node -v'
          sh 'npm install'
        }
      }
    }

    stage('Policy') {
      steps {
        script {
          nexusPolicyEvaluation(
              enableDebugLogging: false,
              iqStage: 'build',
              iqApplication: 'sandbox-application',
              failBuildOnNetworkError: true,
              iqScanPatterns: [
                  [scanPattern: 'package.json'],
                  [scanPattern: 'package-lock.json']
              ],
              callflow: [
                  logLevel: 'DEBUG',
                  jsAnalysis: [
                      enable: true,
                      algorithm: 'NBA',
                      sourceFiles: [
                          [pattern: 'app.js']
                      ]
                  ]
              ]
          )

          archiveArtifacts(
              artifacts: '**/bomxray.log',
              followSymlinks: false
          )
        }
      }
    }
  }
}