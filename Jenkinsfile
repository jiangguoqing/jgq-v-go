@Library('sharedlib')

def tools = new org.devops.tools()


pipeline {
    agent any
//    agent {
//        node {
//            label "master"
//            customWorkspace "$workspace"
//        }
//    }

    options {
        timestamps()
//        skip
    }

    stages {
        stage('GetCode') {
            steps {
                timeout(time:5, unit:"MINUTES"){
                    script{
                        println("check code")
                        tools.PrintMes("test")
                    }
                }
            }
        }

        stage('code scan') {
            steps {
                timeout(time:5, unit:"MINUTES"){
                    script{
                        println("check code")
                    }
                }
            }
        }

        stage('Build') {
            steps {
                timeout(time:5, unit:"MINUTES"){
                    script{
                        println("check code")
                    }
                }
            }
        }
        stage('Test') {
            steps {
                timeout(time:5, unit:"MINUTES"){
                    script{
                        println("check code")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                timeout(time:5, unit:"MINUTES"){
                    script{
                        println("check code")
                    }
                }
            }
        }
    }


post {
    changed {
       echo 'pipeline post changed'
    }
    always {
       echo 'pipeline post always'
    }
    success {
       echo 'pipeline post success'
    }
    // 省略其他条件块

    aborted {
      script{
        currentBuild.description += '\n cancel'
      }
    }
    }
}
