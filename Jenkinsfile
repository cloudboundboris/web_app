pipeline {
  agent any
  tools { go '1.19' }
  stages {
    stage('Checkout SCM') { 
      steps { 
        checkout([ 
          $class: 'GitSCM', 
          branches: [[name: 'main']], 
          userRemoteConfigs: [[ 
            url: 'https://github.com/cloudboundboris/web_app.git', 
            credentialsId: '', 
          ]] 
        ])
      } 
    }
    
    stage('Build') {
      steps {
        echo 'Building application...'
        sh 'go build ./web_app.go'
      }
    }
    stage('Test') {
      steps {
        echo 'Testing application...'
        sh './web_app &'
        sh '''response=$(curl -s -o /dev/null -w "%{http_code}" http://localhost:80) &&
        echo Response is: $response &&
        [ "$response" = "200" ] && exit 0 || exit 1'''

      }
    }
 stage('Deploy') {
    steps {
        echo 'Deploying application...'
        sshagent(credentials: ['creds_srv']) {
          sh 'ssh -o StrictHostKeyChecking=no root@192.168.4.238 "cd /web_app && git pull && go build ./web_app.go && ./web_app &"'
      }
     }
    }
   }
}
