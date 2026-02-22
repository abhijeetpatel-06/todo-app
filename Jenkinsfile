pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }

    environment {
        APP_DIR = "/var/www/nodeapp"
        REPO_URL = "https://github.com/abhijeetpatel-06/todo-app.git"
    }

    stages {

        stage('Check Node.js & npm') {
            steps {
                sh '''
                echo "Checking Node.js..."
                node -v

                echo "Checking npm..."
                npm -v
                '''
            }
        }

        stage('Check MongoDB on Deploy Server') {
    steps {
        sh """
        ssh root@139.59.93.202 '
            echo "Checking MongoDB version..."
            mongod --version || { echo "MongoDB not installed"; exit 1; }

            echo "Checking MongoDB service..."
            systemctl is-active mongod || systemctl is-active mongodb

            echo "Waiting for MongoDB port..."
            timeout 60 bash -c "until nc -z localhost 27017; do sleep 3; done"

            echo "MongoDB is running"
        '
        """
            }
    }

     stage('Check PM2 on Deploy Server') {
    steps {
        sh """
        ssh root@139.59.93.202 '
            echo "Checking PM2 version..."
            pm2 -v || { echo "PM2 not installed"; exit 1; }
        '
        """
        }
    }

     stage('Deploy to Server') {
    steps {
        sh """
        ssh root@139.59.93.202 '
            rm -rf /var/www/nodeapp
            git clone https://github.com/abhijeetpatel-06/todo-app.git /var/www/nodeapp
            cd /var/www/nodeapp
            npm install
            pm2 stop nodeapp || true
            pm2 delete nodeapp || true
            pm2 start index.js --name nodeapp
        '
        """
             }
         }
    }
}