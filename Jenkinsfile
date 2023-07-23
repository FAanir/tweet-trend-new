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
                sh 'mvn clean deploy'
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


