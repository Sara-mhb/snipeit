/* groovylint-disable LineLength, NestedBlockDepth */
pipeline {
  agent { label 'agent-1' }

  options {
    timestamps()
    timeout(time: 35, unit: 'MINUTES')
  }

  environment {
    GITHUB_REPO_URL     = 'https://github.com/Sara-mhb/snipeit.git'
    REPO_BRANCH         = 'main'
    ANSIBLE_VENV        = '/home/jenkins/ansiblevenv'
    PLAYBOOK_NAME       = 'snipeit'
    INVENTORY_PATH      = 'inventory'
    PROJECT_NAME        = 'Snipe-IT Deployment'
  }

  stages {

    stage('Clone Repository') {
      steps {
        echo "########################### Cloning ${env.PROJECT_NAME} from GitHub"
        checkout([
          $class: 'GitSCM',
          branches: [[ name: "*/${env.REPO_BRANCH}" ]],
          userRemoteConfigs: [[
            url: env.GITHUB_REPO_URL
          ]]
        ])
      }
    }

    stage('Verify Inventory') {
      steps {
        echo "########################### Verifying Ansible Inventory"
        sh """#!/bin/bash
          set -e
          source ${env.ANSIBLE_VENV}/bin/activate
          ansible-inventory -i ${env.INVENTORY_PATH} --list
        """
      }
    }

    stage('Run Playbook') {
      steps {
        echo "########################### Executing ${env.PROJECT_NAME} playbook"
        sh """#!/bin/bash
          set -e
          source ${env.ANSIBLE_VENV}/bin/activate
          ansible-playbook \
            -i ${env.INVENTORY_PATH} \
            playbook.yml
        """
      }
    }

  }

  post {
    success {
      echo "########################### Deployment completed successfully!"
    }
    failure {
      echo "########################### Deployment failed. Check logs above."
    }
  }
}
