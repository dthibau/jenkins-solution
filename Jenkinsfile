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
                dir('application/target') {
                    stash includes: '*.jar', name: 'webapp'
                }
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
                    tools {
                        jdk 'JDK8'
                        maven 'M3'
                    }
                    steps {
                        echo 'Tests d integration'
//                        sh 'mvn clean integration-test'
                    }
                    
                }
                 stage('Analyse Sonar') {
                    agent any
                    tools {
                        jdk 'JDK8'
                        maven 'M3'
                    }
                     steps {
                        echo 'Analyse sonar'
//                        sh 'mvn clean test'
//                        script {
//                            def scannerHome = tool 'SONAR4';
//                            withSonarQubeEnv('SONAR_DOCKER') { // If you have configured more than one global server connection, you can specify its name
//                                sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar.properties"                                
//                            }
//                        }

                     }
                    
                }
            }
            
        }
            
        stage('Déploiement intégration') {
            agent any
            options {
                timeout(time: 2, unit: 'MINUTES') 
            }
            input {
                message 'Vers quel provider voulez vous déployer ?'
                ok 'Déployer'
                parameters {
                    choice choices: ['AZURE', 'AMAZON', 'GOOGLE'], description: '', name: 'PROVIDER'
                }
            }
            when {
                not {
                    allOf {
                        environment name: 'PROVIDER', value: 'GOOGLE'
                    }
                }
                beforeAgent true
            }



            steps {
                echo "Déploiement intégration vers $PROVIDER"
                unstash 'webapp'
                script {
                    if ( env.PROVIDER.equals('AZURE') ) {
                        sh "cp *.jar /home/dthibau/Formations/Jenkins/MyWork/Serveur/${env.PROVIDER}.jar"
                    } else if ( env.PROVIDER.equals('AMAZON') ) {
                        sh "cp *.jar /home/dthibau/Formations/Jenkins/MyWork/Serveur/AMAZON.jar"
                    } else {
                        echo "Neither AZURE, neither AMAZON"
                    }
                }
            }
        }

     }
    
}

