pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '192.168.1.21', description: 'Dev Server')
         string(name: 'tomcat_qa', defaultValue: '192.168.1.22', description: 'QA Server')
         string(name: 'tomcat_stage', defaultValue: '192.168.1.23', description: 'Stage Server')
         string(name: 'tomcat_prod', defaultValue: '192.168.1.24', description: 'Production Server')
         string(name: 'tomcat_prod2', defaultValue: '192.168.1.25', description: 'Production Server 2')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage ('Build'){
            steps {
                bat 'C:\\Users\\Greg\\Downloads\\apache-maven-3.5.2-bin\\apache-maven-3.5.2\\bin\\mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                    emailext (to: 'bikeusaland@yahoo.com',
                          subject: "Build Complete '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                          body: """<p>Build Complete: '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                          <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>"""
                    )
                }
                }
            }
        
        stage ('Deploy to Staging'){
             steps {
                bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_dev}:/usr/share/tomcat/webapps"
                bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_qa}:/usr/share/tomcat/webapps"
              }
              post {
                   success {
                    emailext (to: 'bikeusaland@gmail.com',
                          subject: "Build '${env.JOB_NAME} [${env.BUILD_NUMBER}]' deployed, ready for QA",
                          body: """<p>Deploy of '${env.JOB_NAME} [${env.BUILD_NUMBER}]': is ready for QA</p>
                                 Once approved check <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>
                                 to <a href=${env.BUILD_URL}/input/F20ffef031fd33e5d7ce3b683d15e590/proceedEmpty><b>Approve</b></a>
                                 or <a href=${env.BUILD_URL}/input/F20ffef031fd33e5d7ce3b683d15e590/proceedEmpty><b>Abort</b></a>
                                 production deploy</p>"""
                    )
                   }
             }
        }
        stage ("Sanity Check before production"){
             steps {
                 input "Does this work on Dev and QA?"
              }
        }
                stage ("Deploy to Production"){
                    steps {
                        bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_stage}:/usr/share/tomcat/webapps"
                        bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_prod}:/usr/share/tomcat/webapps"
                        bat "C:\\scp -hostkey eb:64:a6:de:60:89:d1:46:09:36:5d:8e:30:19:57:09 -i C:\\\\id.ppk C:\\Jenkins\\workspace\\FullAutomation\\webapp\\target\\*.war pi@${params.tomcat_prod2}:/usr/share/tomcat/webapps"
                    }
                }
            }
}
