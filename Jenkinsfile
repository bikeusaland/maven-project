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
                stage ('Deploy to Staging'){
                    steps {
                        bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_dev}:/usr/share/tomcat/webapps"
                        bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_qa}:/usr/share/tomcat/webapps"
  
                    }
                }
                stage ("Sanity Check before production"){
                    steps {
                        input "Does this work on Dev and QA?"
                    }
                    post {
                        success {
                            mail to: 'bikeusaland@google.com',
                            subject: "build deployed and ready for QA",
                            body: "The deployment is ready for QA....... currentBuild.fullDisplayName {$currentBuild.fullDisplayName}, env {env} "
                        }
                    }
                }
                stage ("Deploy to Production"){
                    steps {
                        bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_stage}:/usr/share/tomcat/webapps"
                        bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_prod}:/usr/share/tomcat/webapps"
                    }
                }
            }
        }
  
}
