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
            }
            steps {
                withSonarQubeEnv('SONAR_LOCAL'){

                  bat "\"${scannerHome}/bin/sonar-scanner\" -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=e75ad33abec089227c8d7cb1af69dfc1945b7cfd -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Aplication.java -Dsonar.java.jdkHome=C:\Program Files\Java\jdk-15.0.2"
                }


                
            }
            
        }
    }
}

