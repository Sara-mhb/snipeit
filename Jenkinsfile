/* groovylint-disable LineLength, NestedBlockDepth */
pipeline {
  agent { label 'agent-1' }

  options {
    timestamps()
    timeout(time: 15, unit: 'MINUTES')
  }

  environment {
    GITHUB_URL          = 'https://github.com/Sara-mhb/snipeit.git'
    GITHUB_CRED_ID      = 'ssh-key-jenkins'
    
    PYTHON_VENV         = '.venv'
    ROLE_NAME           = 'snipeit'
    
    MM_CHANNEL          = 'jenkins@cicd'
    MM_INVENTORY        = 'Snipe-IT Ansible Role'
  }

  stages {

    stage('Clone Repository') {
      steps {
        echo "########################### Cloning ${env.ROLE_NAME} repository"
        checkout([
          $class: 'GitSCM',
          branches: [[ name: "*/main" ]],
          userRemoteConfigs: [[
            url: env.GITHUB_URL,
            credentialsId: env.GITHUB_CRED_ID
          ]]
        ])
      }
    }

    stage('Setup Python Environment') {
      steps {
        echo "########################### Setting up Python virtual environment"
        sh """
          python3 -m venv ${env.PYTHON_VENV}
          . ${env.PYTHON_VENV}/bin/activate
          pip install --upgrade pip
          pip install ansible ansible-lint yamllint
        """
      }
    }

    stage('YAML Lint') {
      steps {
        echo "########################### Running YAML Lint"
        sh """
          . ${env.PYTHON_VENV}/bin/activate
          yamllint . || true
        """
      }
    }

    stage('Ansible Lint') {
      steps {
        echo "########################### Running Ansible Lint"
        sh """
          . ${env.PYTHON_VENV}/bin/activate
          ansible-lint .
        """
      }
    }

  }
  
  post {
    failure {
      echo "Build failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
    }
    success {
      echo "Build succeeded: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
    }
  }
}
