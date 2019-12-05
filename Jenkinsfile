pipeline {
  agent any
  stages {
    stage('build_code') {
      parallel {
        stage('build_code') {
          steps {
            node(label: 'node_build') {
              sh 'bootstrap && configure && make && make install'
            }

            sh 'deploy code'
          }
        }

        stage('deploy_config') {
          steps {
            sh 'rsync -av Share/etc ${node_check_config}:/Share/etc/'
          }
        }

      }
    }

    stage('play') {
      steps {
        sh 'tcpreplay'
        waitUntil() {
          sh '[ -e *.csv ]'
        }

      }
    }

    stage('remtoe_check') {
      steps {
        sh 'curl'
      }
    }

    stage('commit') {
      parallel {
        stage('commit') {
          steps {
            svn(poll: true, url: '192.168.100.10:/jenkins')
          }
        }

        stage('notify') {
          steps {
            mail(subject: 'Jenkins Error', body: 'error message')
          }
        }

      }
    }

    stage('notify') {
      steps {
        catchError(catchInterruptions: true, buildResult: 'FAILURE', message: 'ERROR', stageResult: 'ERROR')
      }
    }

  }
  environment {
    node_compile = '192.168.100.1'
    node_check_config = '192.168.100.2'
    node_check_code = '192.168.100.3'
    node_web_sys = '192.168.100.4'
  }
}