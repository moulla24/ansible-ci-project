pipeline {
    agent any

    stages {
        stage('Install dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    ansible-galaxy collection install community.docker
                    ansible-galaxy collection install ansible.posix
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    . venv/bin/activate
                    ansible-lint .
                '''
            }
        }

        stage('Molecule Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    molecule test
                '''
            }
        }

        stage('Publish Simulation') {
            steps {
                sh '''
                    mkdir -p galaxy-private
                    tar --exclude='.git' --exclude='venv' --exclude='.molecule' -czf galaxy-private/mon_role_web.tar.gz .
                    echo "Role published locally in galaxy-private/"
                '''
            }
        }
    }
}
