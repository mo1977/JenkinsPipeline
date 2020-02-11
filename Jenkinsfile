pipeline {
    agent any

    tools {

        maven 'localMAVEN'
    }

    parameters {
         string(name: 'tomcat_prod', defaultValue: '172.31.20.112', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                            sh "scp **/target/*.war tomcat-admin@${params.tomcat_prod}:/opt/tomcat/webapps"
                    }
                }

  
            }
        }
    }
}
