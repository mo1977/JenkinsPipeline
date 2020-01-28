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
                            sh 'ssh centos@${params.tomcat_dev} rm -rf /webapp/target/webapp.war'
                            sh 'ssh centos@${params.tomcat_dev} mkdir -p /webapp/target/'
                            sh 'scp -r /webapp/target/webapp.war centos@${params.tomcat_dev} /webapp/target/webapp.war'
                            sh 'ssh centos@${params.tomcat_dev} "rm -rf /var/lib/tomcat/webapps/ && mv /webapp/target/webapp.war /var/lib/tomcat/webapps/"'
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                            sh 'ssh centos@${params.tomcat_prod} rm -rf /webapp/target/webapp.war'
                            sh 'ssh centos@${params.tomcat_prod} mkdir -p /webapp/target/'
                            sh 'scp -r /webapp/target/webapp.war centos@${params.tomcat_prod} /webapp/target/webapp.war'
                            sh 'ssh centos@${params.tomcat_prod} "rm -rf /var/lib/tomcat/webapps/ && mv /webapp/target/webapp.war /var/lib/tomcat/webapps/"'
                    }
                }
            }
        }
    }
}
