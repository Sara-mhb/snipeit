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
          # Try to create virtual environment, if it fails install required packages
          if ! python3 -m venv ${env.PYTHON_VENV} 2>/dev/null; then
            echo "Virtual environment creation failed, installing python3-venv..."
            apt update && apt install -y python3-venv
            python3 -m venv ${env.PYTHON_VENV}
          fi
          
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
stage('Setup Python Environment') {
  steps {
    echo "########################### Setting up Python virtual environment"
    sh """
      # Create venv without pip and install pip manually
      python3 -m venv --without-pip ${env.PYTHON_VENV}
      . ${env.PYTHON_VENV}/bin/activate
      
      # Download and install pip manually
      curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
      python get-pip.py
      rm get-pip.py
      
      pip install --upgrade pip
      pip install ansible ansible-lint yamllint
    """
  }
}
