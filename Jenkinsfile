pipeline {
    agent any
    environment {
        buildimage = 'docker image build -t $JOB_NAME:$BUILD_NUMBER /root/myvars/'
        imagetag = 'docker image tag $JOB_NAME:$BUILD_NUMBER shantanudocker1/$JOB_NAME:$BUILD_NUMBER'
        imagetaglatest = 'docker image tag $JOB_NAME:$BUILD_NUMBER shantanudocker1/$JOB_NAME:latest'
        dockerlogin = 'docker login'
        imagepush = 'docker image push shantanudocker1/$JOB_NAME:$BUILD_NUMBER'
        imagepushlatest = 'docker image push shantanudocker1/$JOB_NAME:latest'
        imageremove = 'docker image rmi $JOB_NAME:$BUILD_NUMBER shantanudocker1/$JOB_NAME:latest shantanudocker1/$JOB_NAME:$BUILD_NUMBER'
        runplaybook = 'ansible-playbook /root/mynew/myamaze.yml'
    }
    stages {
        stage("Git Clone") {
            steps {
                git 'https://github.com/Shantanugit/tomcatproject.git'
            }
        }
        
        stage("Code Build") {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage("Code Review") {
            steps {
                sh 'mvn -P metrics checkstyle:checkstyle pmd:pmd'
            }
                
        }
        
        stage("Code test") {
            steps {
                sh 'mvn test'
            }
        }
        
        stage("Code Coverage") {
            steps {
                sh 'mvn cobertura:cobertura'
            }
        }
        
        stage("Code package") {
            steps {
                sh 'mvn package'
            }
        }
        
        stage("Code install") {
            steps {
                sh 'mvn install'
            }
        }
        
        stage("Copy to docker host and create image") {
            steps {
                sh 'scp -ivh /var/lib/jenkins/workspace/mytomc/target/*.war root@172.31.56.169:/root/myvars/'
            }
        }
        
        stage("Build Image") {
            steps {
                withCredentials([string(credentialsId: 'dockerhubuser', variable: 'myuser'), string(credentialsId: 'Dockerhubpasswd', variable: 'mypass')]) {
                    sshagent(['keyname']) {
                        sh "ssh -o strictHostKeyChecking=no root@172.31.56.169 ${buildimage}"
                        sh "ssh -o strictHostKeyChecking=no root@172.31.56.169 ${imagetag}"
                        sh "ssh -o strictHostKeyChecking=no root@172.31.56.169 ${imagetaglatest}"
                        sh "ssh -o strictHostKeyChecking=no root@172.31.56.169 ${dockerlogin} -u ${myuser} -p ${mypass}"
                        sh "ssh -o strictHostKeyChecking=no root@172.31.56.169 ${imagepush}"
                        sh "ssh -o strictHostKeyChecking=no root@172.31.56.169 ${imagepushlatest}"
                        sh "ssh -o strictHostKeyChecking=no root@172.31.56.169 ${imageremove}"
                    }
                }
            }
            
        }
        
        stage("Deployment") {
            steps {
                sshagent(['keyname']) {
                    sh "ssh -o strictHostKeyChecking=no root@172.31.51.114 ${runplaybook}"
                }
            }
        }

    }
    
}

