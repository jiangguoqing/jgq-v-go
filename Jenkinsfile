@Library('sharedlib')

def tools = new org.devops.tools()
String srcurl = "${env.srcurl}"
String branchname = "${env.branchname}"

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
       volumes:
       - name: cache-dir
         emptyDir: {}
        '''.stripIndent()
          }
    }



//读取不到，怎么读取？默认怎么设定
parameters {
  string defaultValue: 'https://github.com/jiangguoqing/jgq-v-go', name: 'srcurl'
//  choice choices: ['main', 'master', 'dev'], name: 'branchname'
}


    triggers {
        GenericTrigger(
            genericVariables: [
              [key: 'ref', value: '$. ref']
            ],
            token: 'secret' ,
            causeString: ' Triggered on $ref' ,
            printContributedVariables: true,
            printPostContent: true
        )


/*  triggers {
    GenericTrigger(
     genericVariables: [
      [key: 'ref', value: '$.ref']
     ],

     causeString: 'Triggered on $ref',

     token: 'abc123',
     tokenCredentialId: '',

     printContributedVariables: true,
     printPostContent: true,

     silentResponse: false,

     shouldNotFlattern: false,

     regexpFilterText: '$ref',
//     regexpFilterExpression: 'refs/heads/' + BRANCH_NAME
    )
  }
*/



/*   environment {        //设置全局变量
      username = 'test'
      password = "123456"
   }
   parameters {         //参数化构建
      choice choices: ['true', 'false'], description: '测试', name: 'test'
      string name: 'NEW_BRANCH', defaultValue: '', description:'', trim: true
    }
*/
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


    stages {
        stage('GWT env') {
            steps {
                sh "echo $ref"
                sh "printenv"
            }
        }
    }

       stage("pull code"){
			steps{
                script {
                tools.PrintMes("获取代码","yellow")
checkout scmGit(branches: [[name: '*/${branchname}']], extensions: [], userRemoteConfigs: [[credentialsId: 'f286958b-d924-4f6e-8720-7a63a2c44717', url: '${srcurl}']])
                }
			}
		}


        stage('Example') {
            steps {
                echo "Hello ${params.PERSON}"
            }
        }




/*    stage('Some step') {
      steps {
        sh "echo $ref"
      }
    }
*/





        stage("stage 1: Test dingding notify") {
            steps {
            	echo 'Test dingding notify'
                script {
                    env.commit = "${sh(script:'git log --oneline --no-merges|head -1', returnStdout: true)}"
                    sh "echo -------------"
                    sh "echo $branchname"
                    sh "echo $commit"
                    sh "env"
                    //变量如何使用？

                }
            }
        }

/*		stage("build & SonarQube analysis") {
            steps {
                script {
                scannerHome = tool 'sonarscanner'
                }
                withSonarQubeEnv('sonar') {
                sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

       stage("Quality Gate"){
			steps{
				timeout(time: 15, unit: 'MINUTES') {
					waitForQualityGate abortPipeline: false
				}
			}
		}

*/
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

