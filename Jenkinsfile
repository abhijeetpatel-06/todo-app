pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }

    environment {
        APP_DIR = "/var/www/nodeapp"
        REPO_URL = "https://github.com/abhijeetpatel-06/todo-app.git"
        SERVER = "root@139.59.93.202"
    }

    stages {

        stage('Deploy to Server') {
            steps {
                sh """
                ssh ${SERVER} '
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