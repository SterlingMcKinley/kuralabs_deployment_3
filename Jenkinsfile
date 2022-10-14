pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''#!/bin/bash
                python3 -m venv test3
                source test3/bin/activate
                pip install pip --upgrade
                pip install -r requirements.txt
                export FLASK_APP=application
                flask run &
                '''
            }
        }
        stage('Test 1') {
            steps {
                sh '''#!/bin/bash
                date
                whoami
                echo "HEY COW, NAME WHAT THING YOU LEARNED IN DEPLOYMENT #2...."
                echo "I LEARNED GROOOOOOOVY LOL"
                '''
            }
        }
        stage('Test 2') {
            steps {
                sh '''#!/bin/bash
                echo "This is a 2nd test script"
                echo "Date : `date`"
                echo "Hostname : `hostname`"
                '''
            }
        }
        stage('test 3') {
            steps {
                sh '''#!/bin/bash
                source test3/bin/activate
                py.test --verbose --junit-xml test-reports/results.xml
                '''
            }

            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Clean') {
            agent { label 'awsDeploy' }
            steps {
                sh '''#!/bin/bash
                if [[ $(ps aux | grep -i "gunicorn" | tr -s " " | head -n 1 | cut -d " " -f 2) != 0 ]]
                then
                ps aux | grep -i "gunicorn" | tr -s " " | head -n 1 | cut -d " " -f 2 > pid.txt
                kill $(cat pid.txt)
                exit 0
                fi
                '''
            }
        }
        stage('Deploy') {
            agent { label 'awsDeploy' }
            steps {
                keepRunning {
                    sh '''#!/bin/bash
                    git clone https://github.com/kura-labs-org/kuralabs_deployment_2.git
                    cd ./kuralabs_deployment_2
                    python3 -m venv test3
                    source test3/bin/activate
                    pip install -r requirements.txt
                    pip install gunicorn
                    JENKINS_NODE_COOKIE=stayAlive gunicorn -w 4 application:app -b 0.0.0.0:5000 --daemon
                    '''
                }
            }
        }
    }
}
