
pipeline {

    agent any

    stages {

        stage('Create Virtual Environment') {
            steps {
                sh '''
                    python3 --version
                    python3 -m venv venv
                    echo "Virtual environment created successfully!"
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    ./venv/bin/python -m pip install --upgrade pip
                    ./venv/bin/pip install -r requirements.txt

                    echo "Dependencies installed successfully!"
                '''
            }
        }

        stage('Run Flask Application') {
            steps {
                sh '''
                    echo "Starting Flask Application..."

                    export JENKINS_NODE_COOKIE=dontKillMe

                    nohup ./venv/bin/python app.py > flask.log 2>&1 &

                    FLASK_PID=$!

                    echo "Flask PID: $FLASK_PID"

                    for i in $(seq 1 30); do

                        if ./venv/bin/python -c "import urllib.request; urllib.request.urlopen('http://127.0.0.1:5000/health', timeout=2)" 2>/dev/null; then

                            echo "Flask application started successfully!"
                            echo "Health check passed!"

                            exit 0

                        fi

                        sleep 1

                    done

                    echo "Flask application failed to start!"

                    cat flask.log

                    exit 1
                '''
            }
        }
    }

    post {

        success {
            echo 'SUCCESS: Flask application started and health check passed!'
        }

        failure {
            echo 'FAILURE: Pipeline execution failed!'
        }
    }
}
