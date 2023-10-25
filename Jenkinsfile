pipeline {
    agent any
    
    stages {
        stage("checkout") {
            steps {
                sh "ls"
                git branch:'main', url: 'https://github.com/npetmark/course3-jenkins-gs-spring-petclinic'
                sh "ls"
            }
        }
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