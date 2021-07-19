pipeline {
    agent any
    stages {
        stage("Git Clone") {
            steps {
                git 'https://github.com/Shantanugit/tomcatproject.git'
            }
        }
        
        stage("Code Compile") {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage("Code Review") {
            steps {
                sh 'mvn -P metrics checkstyle:checkstyle'
            }
        }
        
        stage("Code test") {
            when {
                expression {
                    BRANCH_NAME == 'dev' ||  BRANCH_NAME == 'master'
                }
            }
            steps {
                sh 'mvn test'
            }
        }
        
        stage("Code Coverage") {
            steps {
                sh 'mvn cobertura:cobertura'
            }
        }
        
        stage("Code Package") {
            steps {
                sh 'mvn package'
            }
        }
    }
}
