/* groovylint-disable LineLength, NestedBlockDepth */
pipeline {
  agent { label 'agent-1' }

  options {
    timestamps()
    timeout(time: 35, unit: 'MINUTES')
  }

  environment {
   // Your GitHub repository details (public - no credentials needed)
    GITHUB_REPO_URL     = 'https://github.com/Sara-mhb/snipeit.git'
    REPO_BRANCH         = 'main'  // or 'master' - check your default branch name
    
    // Ansible configuration
    ANSIBLE_VENV        = '/home/jenkins/ansiblevenv'
    PLAYBOOK_NAME       = 'snipeit'
    INVENTORY_PATH      = 'inventory'  // Path to your inventory file in the repo
    
    // Optional: Monitoring/notification settings (customize or remove)
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
            // NO credentialsId needed for public repos!
          ]]
        ])
      }
    }

    stage('Install Ansible Roles') {
      when {
        expression { 
          fileExists('requirements.yml') 
        }
      }
      steps {
        echo "########################### Installing Ansible Roles from requirements.yml"
        sh """
          . ${env.ANSIBLE_VENV}/bin/activate
          ansible-galaxy install --force \
            -r requirements.yml \
            -p roles
        """
      }
    }

    stage('Verify Inventory') {
      steps {
        echo "########################### Verifying Ansible Inventory"
        sh """
          . ${env.ANSIBLE_VENV}/bin/activate
          ansible-inventory -i ${env.INVENTORY_PATH} --list
        """
      }
    }

    stage('Run Playbook') {
      steps {
        echo "########################### Executing ${env.PROJECT_NAME} playbook"
        sh """
          . ${env.ANSIBLE_VENV}/bin/activate
          ansible-playbook \
            -i ${env.INVENTORY_PATH} \
            playbook.yml \
            ${params.ANSIBLE_PARAMS}
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
