pipeline {
    agent {
        kubernetes {
              label 'agent'
            defaultContainer 'build'
   
        }
    }
    
    stages {
      

 stage ('Checkout SCM'){
          git credentialsId: 'git', url: 'https://github.com/amoghazy-organization/19-react-web-app-build.git', branch: 'main'
          container('build') {
                stage('Build a React Webapp') {
                    sh 'CI=false npm run build'             
                }
            }
        }
        stage ('Docker Build'){
          container('build') {
                stage('Build Image') {
                    withDockerRegistry(url: 'https://index.docker.io/v1/', credentialsId: 'docker') {
                        script {
                   
                    def customImage = docker.build("amoghazy/eos-react-webapp:latest")
                    customImage.push()             
                    }
                    }
                }
            }
        }
        stage('Helm Chart Deployment') {
            steps {
                container('build') {
                    dir('charts') {
                        withCredentials([usernamePassword(credentialsId: 'jfrog-cred', usernameVariable: 'username', passwordVariable: 'password')]) {
                          sh ''' 
                            /usr/local/bin/helm package webapp
                            curl -u ecomadmin:$password -T /home/jenkins/agent/workspace/$JOB_NAME/charts/webapp-1.0.tgz "https://triallekevd.jfrog.io/artifactory/ecom-helm-local/webapp-1.0.tgz"
                            '''


                        }
                    }
                }
            }
        }
    }
}








