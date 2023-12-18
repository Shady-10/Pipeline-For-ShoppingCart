pipeline{

    agent any

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

            // post{
            //     success{

            //         echo 'Archieving The Artifact ...'
            //         archiveArtifacts artifacts: '**/*.jar'
            //     }
            // }
        }

        stage('Unit Testing'){

            steps{

                sh 'mvn test'
            }
        }
        stage('Checkstyle'){

            steps{

                sh 'mvn checkstyle:checkstyle'
            }
        }
        // Third Stage (OWASP)

        stage('OWASP Analysis'){

            steps{

                dependencyCheck additionalArguments: '-s ./' , odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        // Fourth Stage (Trivy)

        stage('Trivy') {
            steps {
                sh 'trivy fs .'
            }
        }

    }
}