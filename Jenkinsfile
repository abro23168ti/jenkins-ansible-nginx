pipeline {
    agent any

    environment {
        // Menonaktifkan prompt SSH "Are you sure you want to continue connecting?"
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

                // Menggunakan Credentials 'ansible-ssh-key' yang akan kita buat
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ansible-ssh-key', 
                    keyFileVariable: 'ANSIBLE_KEY', 
                    usernameVariable: 'ANSIBLE_USER')
                ]) {

                    echo "Deploying ke 192.168.0.143 sebagai user: ${env.ANSIBLE_USER}"

                    // Menjalankan perintah ansible-playbook dari dalam kontainer
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