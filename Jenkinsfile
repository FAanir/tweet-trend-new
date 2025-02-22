def registry ='https://aniruddhajfrog.jfrog.io'
def imageName = 'aniruddhajfrog.jfrog.io/aniruddhad-docker-local/ttrend'
def version   = '2.1.3'
pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.3/bin:$PATH"

}


    stages {
        stage("build") {
            steps {
                echo "--------build started-----"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "------build completed--------"
            }
        }

        stage("test"){
            steps{
                echo "----test started------"
                sh 'mvn surefire-report:report'
                echo "----unit test completed---------"
            }
        }

        stage('SonarQube analysis'){
            environment{
                scannerHome = tool 'ani-sonar-scanner'
            }
            steps{
                withSonarQubeEnv('ani-sonar-server'){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage("Quality Gate"){
            steps{
                script{
                    timeout(time: 1, unit:'HOURS'){
                        def qg =waitForQualityGate()
                        if(qg.status != 'OK'){
                            error " pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }

        }

        stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

    stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'latest-token'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }

    stage(" Deploy ") {
       steps {
         script {
            echo '<--------------- Helm Deploy Started --------------->'
            sh 'helm install ttrend ttrend-0.1.0.tgz'
            echo '<--------------- Helm deploy Ends --------------->'
         }
       }
     }
        

        

    }
}


