pipeline {
    agent any

    tools {

        maven 'localMAVEN'
    }

    parameters {
         string(name: 'tomcat_dev', defaultValue: '3.10.117.132', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '35.176.221.45', description: 'Production Server')
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
                            sh "scp -i /home/centos/TomcatKey.pem **/target/*.war centos@${params.tomcat_dev}:/var/lib/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                            sh "scp -i /home/centos/TomcatKey.pem **/target/*.war centos@${params.tomcat_prod}:/var/lib/tomcat/webapps"
                    }
                }
            }
        }
    }
}
