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
//            label "jenkins"
//            customWorkspace "$workspace"
//        }
//    }

    options {
        timestamps()
//        skip
    }




    stages {


		stage("build & SonarQube analysis") {
            steps {
                script {
                scannerHome = tool 'sonarscanner'
                }
                withSonarQubeEnv('sonar') {
                sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }


script {
						Integer waitSeconds = 10
          				Integer timeOutMinutes = 10
          				Integer maxRetry = (timeOutMinutes * 60) / waitSeconds as Integer
                        //  未通过代码检查，中断
                        for (Integer i = 0; i < maxRetry; i++) {
				            try {
				              timeout(time: waitSeconds, unit: 'SECONDS') {
				              	//利用sonar webhook功能通知pipeline代码检测结果，未通过质量阈，pipeline将会fail
				                def qg = waitForQualityGate()
				                echo "${qg.status}"
				                if (qg.status != 'OK') {
				                  error "Sonar quality gate status: ${qg.status}"
				                } else {
				                  i = maxRetry
				                }
				              }
				            } catch (Throwable e) {
				              if (i == maxRetry - 1) {
				                throw e
				              }
				            }
          				}
                    }


//       stage("Quality Gate"){
//			steps{
//				timeout(time: 15, unit: 'MINUTES') {
//					waitForQualityGate abortPipeline: false
//				}
//			}
//		}



        stage("stage 1: Test dingding notify") {
            steps {
            	echo 'Test dingding notify'
                script {
                    env.commit = "${sh(script:'git log --oneline --no-merges|head -1', returnStdout: true)}"
                    sh "echo $commit"
                    //变量如何使用？
                    println(env.commit)
                }
            }
        }

        stage('GetCode') {
            steps {
                timeout(time:5, unit:"MINUTES"){
                    script{
                        println("check code")
                        tools.PrintMes("testjgq","red")
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

//        stage ('test') {
//            steps {
//                parallel (
//                    "unit tests": { sh 'mvn test' },
//                    "integration tests": { sh 'mvn integration-test' }
//                )
//            }
//        }

        stage('Build') {
            steps {
                container('docker'){
                 script {
                    sh "mkdir -p /sys/fs/cgroup/systemd"
                    sh "mount -t cgroup -o none,name=systemd cgroup /sys/fs/cgroup/systemd"
                    sh "chmod +x ./mvnw"
                    tools.Docker_Build()
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
       script{
        echo 'pipeline post always'
       }
    }
    failure {
       script{
            mail to: 'team@example.com', subject: 'Pipeline failed', body: "${env.BUILD_URL}"
       }
    }

    aborted {
      script{
        currentBuild.description += '\n cancel'
      }
    }
    }
}
