pipeline {
   agent none 


    stages {
        stage('Compile et tests') {
            agent any
            tools {
                jdk 'JDK8'
                maven 'M3'
            }
            steps {
                echo 'Unit test et packaging'
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
                success {
                    archiveArtifacts artifacts: 'application/target/*.jar', followSymlinks: false
                }
                failure {
                    mail bcc: '', body: 'http://localhost:8081/job/multi-branche/job/dev', cc: '', from: '', replyTo: '', subject: 'Packaging failed', to: 'david.thibau@gmail.com'
                }
            }             
        }
        stage('Analyse qualité et test intégration') {
            parallel {
                stage('Tests d integration') {
                    agent any
                    steps {
                        echo 'Tests d integration'
                    }
                    
                }
                 stage('Analyse Sonar') {
                     agent any
                     steps {
                        echo 'Analyse sonar'
                     }
                    
                }
            }
            
        }
            
        stage('Déploiement intégration') {
            agent any
            steps {
                echo "Déploiement intégration"
                
            }
        }

     }
    
}

