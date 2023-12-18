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

                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectName=Shopping-Cart \
                    -Dsonar.projectKey=Shopping-Cart \
                    -Dsonar.projectValue=1.0 \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.binaries=. ''' 
                }
            }
        }

        // Seventh Stage (Uploading To Nexus)

        stage('Nexus'){

            steps{

                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '172.31.87.104:8081',
                    groupid: 'QA',
                    repositry: 'Shopping-Cart'
                    credentialsId: 'nexuslogin',
                    artifacts: [
                        artifactId: 'shopping-cart',
                        classifier: '', 
                        file: 'target/shopping-cart-0.0.1-SNAPSHOT.jar',
                        type: 'jar'
                    ]
                )
            }
        }
    }
}