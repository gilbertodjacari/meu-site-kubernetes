pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                echo 'A fazer checkout do codigo...'
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo 'A construir a imagem Docker...'
                sh '''
                    scp -o StrictHostKeyChecking=no -r ${WORKSPACE}/* root@192.168.60.131:/tmp/meu-site/
                    ssh -o StrictHostKeyChecking=no root@192.168.60.131 "cd /tmp/meu-site && docker build -t meu-site:latest ."
                '''
            }
        }
        stage('Import Image') {
            steps {
                echo 'A importar imagem para containerd...'
                sh '''
                    ssh -o StrictHostKeyChecking=no root@192.168.60.131 "docker save meu-site:latest | ctr -n k8s.io images import -"
                '''
            }
        }
        stage('Deploy') {
            steps {
                echo 'A fazer deploy no Kubernetes...'
                sh '/var/jenkins_home/kubectl --kubeconfig=/var/jenkins_home/.kube/config apply -f ${WORKSPACE}/deployment.yaml'
            }
        }
    }
    post {
        success {
            echo 'Deploy concluido com sucesso!'
        }
        failure {
            echo 'Deploy falhou!'
        }
    }
}