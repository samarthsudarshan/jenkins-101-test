pipeline {
        agent any
        
        environment {
            PRISMA_API_URL="https://api2.prismacloud.io"
        }
        
        stages {
            stage('Checkout') {
              steps {
                  git branch: 'master', url: 'https://github.com/samarthsudarshan/jenkins-101-test'
                  stash includes: '**/*', name: 'source'
              }
            }
            stage('Checkov') {
                agent { label 'master' }
                steps {
                    sh 'echo "Hello World"'
                    sh '''
                        echo "Multiline shell steps works too"
                        docker
                    '''
                    withCredentials([string(credentialsId: 'PC_USER', variable: 'pc_user'),string(credentialsId: 'PC_PASSWORD', variable: 'pc_password')]) {
                        script {
                            docker.image('bridgecrew/checkov:latest').inside("--entrypoint=''") {
                              unstash 'source'
                              try {
                                  sh 'checkov -d . --use-enforcement-rules -o cli -o junitxml --output-file-path console,results.xml --bc-api-key ${pc_user}::${pc_password} --repo-id  samarthsudarshan/jenkins-101-test --branch master'
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