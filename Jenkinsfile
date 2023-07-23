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
                sh 'mvn sunfire-report:report'
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

        

    }
}


