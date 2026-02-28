pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }

    environment {
        APP_DIR = "/var/www/nodeapp"
        REPO_URL = "https://github.com/abhijeetpatel-06/todo-app.git"
        SERVER = "root@64.227.184.145"
    }

    stages {

        stage('Deploy to Server') {
            steps {
                sh """
                ssh ${SERVER} '
                    
                    echo "===== CHECKING MONGODB ====="

                    # Check if mongod exists
                    if ! command -v mongod > /dev/null; then
                        echo "MongoDB not installed. Installing MongoDB..."

                        apt update -y
                        apt install -y gnupg curl

                        curl -fsSL https://pgp.mongodb.com/server-6.0.asc | \
                        gpg -o /usr/share/keyrings/mongodb-server-6.0.gpg \
                        --dearmor

                        echo "deb [ arch=amd64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] \
                        https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | \
                        tee /etc/apt/sources.list.d/mongodb-org-6.0.list

                        apt update -y
                        apt install -y mongodb-org
                    else
                        echo "MongoDB already installed."
                    fi

                    # Start and enable MongoDB
                    systemctl start mongod
                    systemctl enable mongod

                    systemctl status mongod --no-pager

                    echo "===== MONGODB READY ====="

                    # If app directory does not exist → clone
                    if [ ! -d "${APP_DIR}" ]; then
                        git clone ${REPO_URL} ${APP_DIR}
                    else
                        cd ${APP_DIR}
                        git pull origin main
                    fi

                    cd ${APP_DIR}

                    # Install dependencies only if node_modules missing
                    if [ ! -d "node_modules" ]; then
                        echo "Installing dependencies..."
                        npm install
                    else
                        echo "Dependencies already installed. Skipping npm install."
                    fi

                    # Restart or Start PM2
                    pm2 describe nodeapp > /dev/null
                    if [ \$? -eq 0 ]; then
                        pm2 restart nodeapp
                    else
                        pm2 start index.js --name nodeapp
                    fi
                '
                """
            }
        }
    }
}