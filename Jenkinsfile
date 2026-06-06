pipeline {
  agent any
  environment {
    DOCKERHUB_USER = 'brahimhamdi'
    IMAGE_TAG      = "${env.BUILD_NUMBER}"
    DOCKERHUB      = credentials('dockerhub')   // -> _USR and _PSW
  }
  options { timestamps() }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/brahimhamdi/dockercoins.git',
            branch: 'main'
      }
    }

    stage('Build') {
      steps {
        script {
          for (svc in ['rng','hasher','worker','webui']) {
            sh "docker build -t $DOCKERHUB_USER/${svc}:$IMAGE_TAG ./${svc}"
          }
        }
      }
    }

    stage('Test') {
      steps {
        sh '''
          docker run -d -p 8001:80 --name rng_test \
                 $DOCKERHUB_USER/rng:$IMAGE_TAG
          sleep 3
          curl -sf http://localhost:8001/10 > /dev/null \
                 && echo "rng smoke test OK"
          docker rm -f rng_test
        '''
      }
    }

    stage('Push') {
      steps {
        sh 'echo "$DOCKERHUB_PSW" | docker login \
                -u "$DOCKERHUB_USR" --password-stdin'
        script {
          for (svc in ['rng','hasher','worker','webui']) {
            sh "docker push $DOCKERHUB_USER/${svc}:$IMAGE_TAG"
            sh "docker tag  $DOCKERHUB_USER/${svc}:$IMAGE_TAG \
                    $DOCKERHUB_USER/${svc}:latest"
            sh "docker push $DOCKERHUB_USER/${svc}:latest"
          }
        }
      }
    }

    stage('Deploy to staging') {
      steps { sh 'docker compose up -d' }
    }

    stage('Approve production') {
      steps { input message: 'Deploy to production?', ok: 'Deploy' }
    }

    stage('Deploy to production') {
      steps {
        withKubeConfig([credentialsId: 'kubeconfig']) {
          sh 'kubectl apply -f k8s/ -n dockercoins'
        }
      }
    }
  }

  post {
    always  { sh 'docker logout || true' }
    success { echo "Build $IMAGE_TAG delivered successfully." }
    failure { echo 'Pipeline failed - check the stage view.' }
  }
}
