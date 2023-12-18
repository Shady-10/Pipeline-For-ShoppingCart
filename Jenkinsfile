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

        // Third Stage (CheckStyle With Maven)

        stage('CheckStyle'){

            steps{

                sh 'mvn checkstyle:checkstyle'
            }
        }

        // Fourth Stage (OWASP)

        stage('OWASP Analysis'){

            steps{

                dependencyCheck additionalArguments: '-s ./' , odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        // Fifth Stage (Trivy)

        stage('Trivy') {
            steps {
                sh 'trivy fs .'
            }
        }


        // Sixth Stage (SonarQube Analysis)

        stage('SonarQube'){


            environment{

                scannerHome = tool 'SONAR4.7'
            }
            steps{

                withSonarQubeEnv('SONAR'){

                    sh '''${scannerHome}/bin/sonar-scanne -Dsonar.projectName=Shopping-Cart \
                    -Dsonar.projectKey=Shopping-Cart \
                    -Dsonar.projectValue=1.0 \
                    -Dsonar.java.binaries=. ''' 
                }
            }
        }
    }
}