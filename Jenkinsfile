pipeline {
        agent any
        
        environment {
            PRISMA_API_URL="https://api2.prismacloud.io"
        }

        triggers {
            pollSCM '* * * * *'
        }
        
        stages {
            stage('Checkout') {
              steps {
                  git branch: 'master', url: 'https://github.com/samarthsudarshan/jenkins-101-test'
                  stash includes: '**/*', name: 'source'
              }
            }
            stage('Checkov') {
                steps {
                    withCredentials([string(credentialsId: 'PC_USER', variable: 'pc_user'),string(credentialsId: 'PC_PASSWORD', variable: 'pc_password')]) {
                        script {
                            docker.image('bridgecrew/checkov:latest').withRun("-v /var/run/docker.sock:/var/run/docker.sock --privileged").inside("--entrypoint=''") {
                              unstash 'source'
                              try {
                                  sh 'checkov -d . --use-enforcement-rules -o cli -o junitxml --output-file-path console,results.xml --bc-api-key ${pc_user}::${pc_password} --repo-id  samarthsudarshan/jenkins-101-test --branch main'
                                  junit skipPublishingChecks: true, testResults: 'results.xml'
                              } catch (err) {
                                  junit skipPublishingChecks: true, testResults: 'results.xml'
                                  throw err
                              }
                            }
                        }
                    }
                }
            }
        }
        options {
            preserveStashes()
            timestamps()
        }
    }