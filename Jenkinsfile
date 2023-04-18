@Library('sharedlib')

def tools = new org.devops.tools()


pipeline {
    agent  {
      kubernetes {
        label 'hello'
        yaml '''
apiVersion: v1
kind: Pod
metadata:
   name: clean-ci
spec:
   containers:
   - name: docker
     image: 'docker:stable-dind'
     command:
     - dockerd
     - --host=unix:///var/run/docker.sock
     - --host=tcp://0.0.0.0:8000
     - --insecure-registry=167.71.195.24:30002
     securityContext:
       privileged: true
     volumeMounts:
     - mountPath: /var/run
       name: cache-dir
   - name: clean-ci
     image: 'docker:stable'
     command: ["/bin/sh"]
     args: ["-c","while true; do sleep 86400; done"]
     volumeMounts:
     - mountPath: /var/run
       name: cache-dir

   - name: go-lint
     image: 'golangci/golangci-lint'
     command: ["/bin/sh"]
     args: ["-c","while true; do sleep 86400; done"]
     volumeMounts:
     - mountPath: /var/run
       name: cache-dir


   - name: trivy
     image: 'aquasec/trivy:0.21.1'
     command: ["/bin/sh"]
     args: ["-c","while true; do sleep 86400; done"]
     volumeMounts:
     - mountPath: /var/run
       name: cache-dir


   volumes:
   - name: cache-dir
     emptyDir: {}
        '''.stripIndent()
          }
    }



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
                        tools.PrintMes("testjgq")
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
              container ('docker'){
              sh 'docker build -t 167.71.195.24:30002/myharbor/gojgq-dev-${GIT_BRANCH}-${GIT_SHA:0:7}-$(date +%s):v5 .'
              sh 'docker login 167.71.195.24:30002 -u jgq -p Jgq123456'
              sh 'docker push 167.71.195.24:30002/myharbor/gojgq-dev:v5'
              sh '''
              echo "you did it!!!!!!!  yes!!"
              '''
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
