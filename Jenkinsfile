pipeline {
    agent any
    stages {
        stage ('Build Backend') {

            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Tests') {

            steps {
                bat 'mvn test'
            }
            
        }
         stage ('Sonar Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
                jdkHome= tool 'JAVA_LOCAL'
            }
            steps {
                withSonarQubeEnv('SONAR_LOCAL'){

                  bat "\"${scannerHome}/bin/sonar-scanner\" -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=e75ad33abec089227c8d7cb1af69dfc1945b7cfd -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Aplication.java"
                }


                
            }
            
        }
        stage ('Quality Gate') {
            
            steps {
                sleep(5)
                timeout(time:1, unit: 'MINUTES') {
                   waitForQualityGate abortPipeline: true
                
                }
                

            }


                
        }

        stage ('Deploy Backend') {
            steps{
                deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war' 
            }
            
        }
        
    }
}


