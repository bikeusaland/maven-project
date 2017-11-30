pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '192.168.1.21', description: 'Dev Server')
         string(name: 'tomcat_qa', defaultValue: '192.168.1.22', description: 'QA Server')
         string(name: 'tomcat_stage', defaultValue: '192.168.1.23', description: 'Stage Server')
         string(name: 'tomcat_prod', defaultValue: '192.168.1.24', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                bat 'C:\\Users\\Greg\\Downloads\\apache-maven-3.5.2-bin\\apache-maven-3.5.2\\bin\\mvn clean package'
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
                        bat "scp -i /home/jenkins/id.rsa **/target/*.war pi@${params.tomcat_dev}:/usr/share/tomcat/webapps"
                        bat "scp -i /home/jenkins/id.rsa **/target/*.war pi@${params.tomcat_qa}:/usr/share/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        bat "scp -i /home/jenkins/id.rsa **/target/*.war pi@${params.tomcat_stage}:/usr/share/tomcat/webapps"
                        bat "scp -i /home/jenkins/id.rsa **/target/*.war pi@${params.tomcat_prod}:/usr/share/tomcat/webapps"
                    }
                }
            }
        }
    }
}
