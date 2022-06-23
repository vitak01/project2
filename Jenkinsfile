pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: podman
            image: quay.io/podman/stable
            command:
            - cat
            tty: true
            securityContext:
               privileged: true
            
       '''
    }
  }
  environment {
        DOCKERHUB_CREDENTIALS= credentials('dockerhub-pwd')
  }
  stages {
    stage('test -version podman inside docker') {
      steps {
        container('podman') {
          sh 'podman --version'
          sh 'whoami'
          // sh 'podman pull docker.io/library/httpd'
        }
      }
    }  
    stage('git clone') {
      steps {
        container('podman') {
          git branch: 'main', url: 'https://github.com/vitak01/project2.git'
              echo "Clone Successfuly !!!"
        }
      }
    }
    stage('docker build') {
      steps {
        container('podman') {
          sh " podman build -t vitak03/app1:$BUILD_NUMBER ."
          echo " Build image Completed "
        }
      }
    }
    stage('Login to Docker Hub ') {
      steps{
        container('podman') {
           sh 'echo $DOCKERHUB_CREDENTIALS_PSW | podman login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
           echo "Login Completed"
        }
      }
    }
    stage('Push in Docker Hub ') {
      steps{
        container('podman') {
           sh 'podman push docker.io/vitak03/app1:$BUILD_NUMBER'
           echo "Login Completed"
        }
      }
    }
  }
  post {
    always {
           sh 'podman logout'
    }
  }
}
