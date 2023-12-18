pipeline{

    agent ant 

    tools{

        jdk "OracleJDK11"
        maven "MAVEN3"
    }

    stages{

        // First Stage (Fetching The Code)

        stage('Fetching The Code'){

            steps{

                git branch: 'main' , url: 'https://github.com/Shady-10/Pipeline-For-ShoppingCart.git'
            }
        }

        // Second Stage (Buikding The Artifact)

        stage('Building The Artifact'){

            steps{

                sh 'mvn install -DskipTests'
            }

            post{
                success{

                    echo 'Archieving The Artifact ...'
                    archiveArtifacts artifacts: '**/*.jar'
                }
            }
        }
        
    }
}