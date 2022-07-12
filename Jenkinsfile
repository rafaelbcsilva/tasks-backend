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
                sleep(11)
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
        stage ('API Test') {
            steps{
                dir('api-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/rafaelbcsilva/tasks-api-test'
                    bat 'mvn test'
                }
                
            }
            
        }
        stage ('Deploy Frontend') {
            steps{
                dir('frontEnd') {

                git changelog: false, credentialsId: 'github_login', url: 'https://github.com/rafaelbcsilva/tasks-frontend'
                bat 'mvn clean package'
                deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war' 
                }
                
            }
            
        }
        stage ('Functional Test') {
            steps{
                dir('functional-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/rafaelbcsilva/tasks-functional-tests'
                    bat 'mvn test'
                }
                
            }
            
        }
        stage ('Deploy Prod'){

            steps {
                bat 'docker-compose build'
                bat 'docker-compose up -d'
            }
        }
        stage ('Health Check') {
            steps{
                sleep(10)
                dir('functional-test') {
                    bat 'mvn verify -Dskip.surefire.tests'
                }
                
            }
            
        }
    }
    post {
        always {
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml, functional-test/target/surefire-reports/*.xml, functional-test/target/failsafe-reports/*.xml'
            archiveArtifacts artifacts: 'target/tasks-backend.war, FrontEnd/target/tasks.war', followSymlinks: false, onlyIfSuccessful: true
        }
        unsuccessful {
            emailext attachLog: true, body: 'See the attached Log!!', subject: 'Build $BUILD_NUMBER has Failed', to: 'rafael.bcsilva+jenkins@gmail.com'

        }
        fixed {
            emailext attachLog: true, body: 'See the attached Log!!', subject: 'Build $BUILD_NUMBER is Fine =)', to: 'rafael.bcsilva+jenkins@gmail.com'

        }

    }
}


