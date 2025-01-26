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
        withCredentials([sshUserPrivateKey(credentialsId: '7fa5ee1f-3a15-4854-978b-b85d2d952d26', keyFileVariable: 'EC2_KEY_PATH', usernameVariable: 'EC2_USER')]) {
            docker.image('python:2-alpine').inside('--user root') {
                sh '''
                    apk add --no-cache openssh

                    EC2_HOST="18.141.209.202"
                    DEPLOY_DIR="/home/ec2-user/deploy"
                    
                    scp -i ${EC2_KEY_PATH} -o StrictHostKeyChecking=no sources/add2vals.py ${EC2_USER}@${EC2_HOST}:${DEPLOY_DIR}

                    ssh -i ${EC2_KEY_PATH} -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} "cd ${DEPLOY_DIR} && python3 add2vals.py 10 20"
                '''
            }
        }
    }
}
