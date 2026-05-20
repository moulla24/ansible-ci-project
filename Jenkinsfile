pipeline {
    agent any

    environment {
        PIP_CACHE_DIR = "${WORKSPACE}/.pip-cache"
    }

    stages {
        stage('Install dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install --cache-dir $PIP_CACHE_DIR -r requirements.txt
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
                    molecule destroy || true
                    molecule test --destroy always
                '''
            }
        }

        stage('Publish Simulation') {
            steps {
                sh '''
                    mkdir -p galaxy-private
                    tar --exclude='.git' --exclude='venv' --exclude='.molecule' --exclude='.pip-cache' -czf galaxy-private/mon_role_web.tar.gz .
                    echo "Role published locally in galaxy-private/"
                '''
            }
        }
    }

    post {
        always {
            sh '''
                . venv/bin/activate || true
                molecule destroy || true
            '''
        }
    }
}
