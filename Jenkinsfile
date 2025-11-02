pipeline {
  agent { label 'jenkins-slave-1' }
  
  stages {
    stage('Clone') {
      steps {
        git url: 'https://github.com/your-username/repo.git', branch: 'main'
      }
    }
    
    stage('Deploy to .20') {
      steps {
        sh '''
          # Deploy to the other VM
          scp -r * jenkins@10.137.208.20:/var/www/html/
        '''
      }
    }
  }
}
