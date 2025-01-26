node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            try {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } finally {
                junit 'test-reports/results.xml'
            }
        }
    }
    
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }

    stage('Deploy') {
        docker.image('python:2-alpine').inside {
            sh '''
                echo "Starting Python application..."
                python sources/add2vals.py 10 20 &
                echo "Application is running for 60 seconds..."
                sleep 60
                APP_PID=$!
                echo "Stopping Python application..."
                wait $APP_PID
            '''
        }
    }
}
