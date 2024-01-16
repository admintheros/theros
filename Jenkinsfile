pipeline {
    agent any

    environment {
        MASTER_USERNAME = 'admin'
        MASTER_PASSWORD = 'w9GGvTAYK2M6NZ02TjJLCopy'
        ENDPOINT = 'database-1.cr4ikos48f3f.us-east-1.rds.amazonaws.com'
        DB_NAME = 'sua_base_de_dados'
        DB_USER = MASTER_USERNAME
        DB_PASSWORD = MASTER_PASSWORD
        GIT_USERNAME = 'therosteste'
        GIT_EMAIL = 'therosteste@gmail.com'
        GIT_PRIVATE_KEY = 'conteudo_da_sua_chave_privada'  // Substitua com o conteúdo real da chave privada
        REPO_NAME = 'therosteste'
        REPO_URL = "git@github.com:$GIT_USERNAME/$REPO_NAME.git"
    }

    stages {
        stage('Configuração') {
            steps {
                sh 'sudo yum update -y'
                sh 'sudo yum install -y mariadb'
                sh 'sudo yum install -y git'
                sh 'sudo yum install -y python3'
                sh 'sudo alternatives --set python /usr/bin/python3'
                sh 'sudo yum install -y python3-pip'
            }
        }

        stage('Configuração do MySQL') {
            steps {
                sh "mysql -h $ENDPOINT -u $MASTER_USERNAME -p$MASTER_PASSWORD -e 'CREATE DATABASE IF NOT EXISTS $DB_NAME;'"
                sh "mysql -h $ENDPOINT -u $MASTER_USERNAME -p$MASTER_PASSWORD -e 'CREATE USER IF NOT EXISTS '\''$DB_USER'\''@'\''%'\'' IDENTIFIED BY '\''$DB_PASSWORD'\'';'"
                sh "mysql -h $ENDPOINT -u $MASTER_USERNAME -p$MASTER_PASSWORD -e 'GRANT ALL PRIVILEGES ON $DB_NAME.* TO '\''$DB_USER'\''@'\''%'\'';'"
                sh "mysql -h $ENDPOINT -u $MASTER_USERNAME -p$MASTER_PASSWORD -e 'FLUSH PRIVILEGES;'"
            }
        }

        stage('Configuração do Git') {
            steps {
                sh 'mkdir -p /home/ec2-user/.ssh'
                sh 'echo "$GIT_PRIVATE_KEY" > /home/ec2-user/.ssh/id_rsa'
                sh 'chmod 600 /home/ec2-user/.ssh/id_rsa'
                sh "git config --global user.name '$GIT_USERNAME'"
                sh "git config --global user.email '$GIT_EMAIL'"
            }
        }

        stage('Clone do Repositório') {
            steps {
                sh "git clone $REPO_URL"
                sh "cd $REPO_NAME"
            }
        }

        stage('Instalação de Dependências da Aplicação') {
            steps {
                sh 'pip3 install -r requirements.txt'
            }
        }

        stage('Migrações do Banco de Dados') {
            steps {
                sh 'python3 manage.py db init'
                sh 'python3 manage.py db migrate'
                sh 'python3 manage.py db upgrade'
            }
        }

        stage('Execução da Aplicação') {
            steps {
                sh 'nohup python3 app.py &'
            }
        }
    }
}
