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
            when{
                expression{
                    currentBuild.resultIsBetterOrEqualTo('SUCCESS')

                }
        }
            steps{

                // Provide Your Own Neccessary Data

                nexusArtifactUploader(
                    nexusVersion: '',
                    protocol: 'http',
                    nexusUrl: '',
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: '',
                    credentialsId: '',
                    artifacts: [
                        [artifactId: 'shopping-cart',
                        classifier: '', 
                        file: 'target/shopping-cart-0.0.1-SNAPSHOT.jar',
                        type: 'jar']
                    ]
                )
            }
        }

        // Eigth Stage (Building Docker Image)

        stage('Docker Image'){

            steps{

                script{

                    withDockerRegistry(credentialsId: 'dockerlogin' , toolName: 'Docker'){

                        sh 'docker buildx build -t shopping-cart:dev -f docker/Dockerfile .'
                        sh 'docker tag shopping-cart:dev shady25/shopping-cart:dev'
                    }
                }
            }
        }

        // Eigth Stage (Pushing The Docker Image To DockerHub)

        stage('Pushing The Docker Image'){

            steps{

                script{

                    withDockerRegistry(credentialsId: 'dockerlogin' , toolName: 'Docker'){

                        sh 'docker push shady25/shopping-cart:dev'
                    }
                }
            }
        }

        // Ninth Stage (Deploying The Application)

        stage('Deploy'){

            steps{

                script{

                    withDockerRegistry(credentialsId: 'dockerlogin' , toolName: 'Docker'){

                        sh 'docker run -d --name cart -p 8070:8070 shady25/shopping-cart:dev'
                    }
                }
            }
        }

    }
}