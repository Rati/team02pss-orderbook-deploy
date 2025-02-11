pipeline {
  agent none
  stages {
    stage('Build Trading Front End') {
      when{ 
          anyOf {
            changeset 'src/nginx/**'
            expression {
              currentBuild.number == 1
            }
          }
      }
      agent {
        node {
          label 'kaniko'
        }
      }
      steps {
        container(name: 'kaniko') {
          sh '''echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json
/kaniko/executor -f `pwd`/Dockerfiles/Dockerfile_nginx -c `pwd` --insecure --skip-tls-verify --cache=false --destination=${ECR_REPO}:${JOB_NAME}fe-dev-${BUILD_NUMBER}'''
        }
      }
    }

    stage('Build and Publish DB') {
      when{ 
          anyOf {
            changeset 'src/mysql/**'
            expression {
              currentBuild.number == 1
            }
          }
      }
      agent {
        node {
          label 'kaniko'
        }
      }
      steps {
        container(name: 'kaniko') {
          sh '''echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json
/kaniko/executor -f `pwd`/Dockerfiles/Dockerfile_mysql -c `pwd` --insecure --skip-tls-verify --cache=false --destination=${ECR_REPO}:${JOB_NAME}db-dev-${BUILD_NUMBER}'''
        }
      }
    }
 
    
    stage('Build and Publish API') {
      when{ 
          anyOf {
            changeset 'src/fastapi/**'
            expression {
              currentBuild.number == 1
            }
          }
      }
      agent {
        node {
          label 'kaniko'
        }
      }
      steps {
        container(name: 'kaniko') {
          sh '''echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json
/kaniko/executor -f `pwd`/Dockerfiles/Dockerfile_fastapi -c `pwd` --insecure --skip-tls-verify --cache=false --destination=${ECR_REPO}:${JOB_NAME}api-dev-${BUILD_NUMBER}'''
        }
      }
    }
    stage('Currency API') {
      when{ 
          anyOf {
            changeset 'src/currencyAPI/**'
            expression {
              currentBuild.number == 1
            }
          }
      }
      agent {
        node {
          label 'kaniko'
        }
      }
      steps {
        container(name: 'kaniko') {
          sh '''echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json
/kaniko/executor -f `pwd`/Dockerfiles/Dockerfile_currencyAPI -c `pwd` --insecure --skip-tls-verify --cache=false --destination=${ECR_REPO}:${JOB_NAME}currency-api-dev-${BUILD_NUMBER}'''
        }
      }
    }
  }
 
  environment {
    ECR_REPO = '108174090253.dkr.ecr.us-east-1.amazonaws.com/production-support-course'
  }
  triggers {
    pollSCM('*/10 * * * 1-5')
  }
}


