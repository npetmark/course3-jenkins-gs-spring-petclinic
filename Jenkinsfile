pipeline {
    agent any
    
    stages {
        stage("build") {
            steps {
                sh "./mvnw package"                
            }
        }
        stage("capture") {
            steps {
                archiveArtifacts '**/target/*.jar'
            }
        }
        stage("test & coverage") {
            steps {
                junit '**/target/surefire-reports/TEST*.xml'
                jacoco()
            }
        }

        stage("deploy") {
            agent { 
                docker { 
                    image 'nmark/petclinic' 
                    args ''' 
                            --rm=false
                            -p 9000:9000
                            '''
                }
            }
            steps {
                copyArtifacts filter: '**/*.jar', fingerprintArtifacts: true, projectName: 'scm-declarative', selector: upstream(fallbackToLastSuccessful: true), target: './target'
                sh 'java -Dserver.port=9000 -jar **/spring-petclinic-3.1.0-SNAPSHOT.jar &'
            }
        }
    }
    post {
        always {
            emailext body: "${env.BUILD_URL}\n${currentBuild.absoluteUrl}",
                to: 'nikolay.markov@technocloudlab.com',
                recipientProviders: [previous()], 
                subject: "${currentBuild.currentResult}: Job ${env.JOB_NAME} [${env.BUILD_NUMBER}]"
        }
    }
}
