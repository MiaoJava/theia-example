/**
 * This Jenkinsfile builds Theia across the major OS platforms
 */

pipeline {
    agent none
    environment {
        BUILD_TIMEOUT = 180
    }
    stages {
        stage('Installers') {
            parallel {
                stage('Create Linux Installer') {
                    agent {
                        kubernetes {
                            label 'node-pod'
                            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: node
    image: thegecko/theia-dev
    command:
    - cat
    tty: true
    resources:
      limits:
        memory: "4Gi"
        cpu: "2"
      requests:
        memory: "4Gi"
        cpu: "2"
    volumeMounts:
    - name: yarn-cache
      mountPath: /.cache
    - name: electron-cache
      mountPath: /.electron-gyp
  volumes:
  - name: yarn-cache
    emptyDir: {}
  - name: electron-cache
    emptyDir: {}
"""
                        }
                    }
                    steps {
                        timeout(time: "${env.BUILD_TIMEOUT}") {
                            container('node') {
                                sh "printenv"
                                sh "yarn cache clean"
                                sh "yarn --frozen-lockfile --force"
                                sh "yarn package"
                            }
                        }
                    }
                }
                stage('Create Mac Installer') {
                    agent {
                        label 'macos'
                    }
                    steps {
                        timeout(time: "${env.BUILD_TIMEOUT}") {
                            sh "printenv"
                            sh "yarn cache clean"
                            sh "yarn --frozen-lockfile --force"
                            sh "yarn package"
                        }
                    }
                }
                stage('Create Windows Installer') {
                    agent {
                        label 'windows'
                    }
                    steps {
                        timeout(time: "${env.BUILD_TIMEOUT}") {
                            bat "set"
                            bat "yarn cache clean"
                            bat "yarn --frozen-lockfile --force"
                            bat "yarn package"
                        }
                    }
                }
            }
        }
    }
}
