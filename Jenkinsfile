pipeline {
    agent any

    environment {
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Mengambil kode dari GitHub...'
                checkout scm
            }
        }

        stage('Run Ansible Deployment') {
            steps {
                echo 'Memulai deployment Ansible...'

                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ansible-ssh-key', 
                    keyFileVariable: 'ANSIBLE_KEY', 
                    usernameVariable: 'ANSIBLE_USER')
                ]) {

                    echo "Deploying ke 192.168.0.143 sebagai user: ${env.ANSIBLE_USER}"

                    sh '''
                    ansible-playbook \
                        -i inventory.ini \
                        playbook.yml \
                        -u $ANSIBLE_USER \
                        --private-key $ANSIBLE_KEY
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "DEPLOYMENT SUKSES!"
            echo "Cek di http://192.168.0.143"
        }
        failure {
            echo "DEPLOYMENT GAGAL."
        }
    }
}
