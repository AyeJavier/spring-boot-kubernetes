pipeline {
    agent any

    tools {
        maven 'Maven'
        nodejs 'NodeJs'
    }
  
    stages {
        stage('initial'){
            steps{
                figlet 'Inital'
                
             sh '''
              echo "PATH = ${PATH}"
              echo "M2_HOME = ${M2_HOME}"
              '''
            }
        }

        // stage('Compile'){
        //     steps{
        //         figlet 'Compile'
        //         sh 'mvn clean compile -e'
        //     }
        // }
        
        // stage('Test'){
        //     steps{
        //         figlet 'Test'
        //         sh 'mvn clean test -e'
        //     }
        // }
        // stage('SAST'){
        //    steps{
        //        figlet 'SonarQube'
        //        script{
        //            def scannerHome = tool 'SonarQube Scanner'
                   
        //            withSonarQubeEnv('Sonar Server'){
        //                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=soyphea -Dsonar.sources=. -Dsonar.projectBaseDir=${env.WORKSPACE} -Dsonar.java.binaries=target/classes -Dsonar.exclusions='**/*/test/**/*, **/*/acceptance-test/**/*, **/*.html,scripts/**/*,k8s/**/*,*.go'"
        //            }
        //        }
        //    }
        //   }
        // stage('SCA'){
        //     steps{
        //         figlet 'Dependency-Check'
        //         sh 'mvn org.owasp:dependency-check-maven:purge'
        //         sh 'mvn org.owasp:dependency-check-maven:check'
                
        //         archiveArtifacts artifacts: 'target/**', followSymlinks: false
        //     }
        // }
        stage('DAST'){
            steps{
                figlet 'Owasp Zap DAST'
        		
        		script{
        		   env.DOCKER = tool "Docker"
        		   env.DOCKER_EXEC = "${DOCKER}/bin/docker"
        		   env.TARGET = 'http://zero.webappsecurity.com/'
        	
        		    // sh '${DOCKER_EXEC} rm -f zap2'
        		    sh '${DOCKER_EXEC} pull owasp/zap2docker-stable'
                    sh '${DOCKER_EXEC} run --add-host="localhost:192.168.88.177" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8093:8093 -d owasp/zap2docker-stable zap.sh -daemon -port 8093 -host 0.0.0.0 -config api.disablekey=true'
                    sh '${DOCKER_EXEC} run --add-host="localhost:192.168.88.177" -v /Users/jcurguan/proyectos/propio/cursos/devsecops/20211015/jenkins_home/tools:/zap/wrk --rm -i owasp/zap2docker-stable zap-baseline.py -t "https://demo.testfire.net/" -I -r zap_baseline_report.html -l PASS'	
        		   
        		   publishHTML([
        				    allowMissing: false,
        				    alwaysLinkToLastBuild: false,
        				    keepAll: false,
        				    reportDir: '/var/jenkins_home/tools',
        				    reportFiles: 'zap_baseline_report.html',
        				    reportName: 'HTML Report',
        				    reportTitles: ''])
        		}
            }
        }
        
        stage('Scan Docker'){
                            steps{
                                figlet 'Scan Docker'
                                script{
                                    env.DOCKER = tool "Docker"
                                        env.DOCKER_EXEC = "${DOCKER}/bin/docker"

                                    sh '''
                                        ${DOCKER_EXEC} run --rm -v $(pwd):/root/.cache/ aquasec/trivy python:3.4-alpine
                                    '''

                                        sh '${DOCKER_EXEC} rmi aquasec/trivy'
                                }
                            }
        }
    }
    
           
}