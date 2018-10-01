pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '172.20.0.3', description: 'Staging Server')
         string(name: 'tomcat_dev_port', defaultValue: '22', description: 'Staging Server Port')
         string(name: 'tomcat_prod', defaultValue: '172.20.0.2', description: 'Production Server')
         string(name: 'tomcat_prod_port', defaultValue: '22', description: 'Production Server Port')
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
                        sh "scp -i /var/jenkins_home/tomcat-key.pub -P ${params.tomcat_dev_port} **/target/*.war root@${params.tomcat_dev}:/usr/local/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /var/jenkins_home/tomcat-key.pub -P ${params.tomcat_prod_port} **/target/*.war root@${params.tomcat_prod}:/usr/local/tomcat/webapps"
                    }
                }
            }
        }
    }
}
