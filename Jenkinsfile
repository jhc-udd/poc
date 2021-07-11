pipeline {
    agent {label 'docker'}

    environment {
        PATH = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
        DOCKER_USER = credentials('user-dockerhub')
        DOCKER_PASS = credentials('pass-dockerhub')
    }

    stages {
        stage('Clone sources') {
            steps {
                git url: 'https://github.com/jhc-udd/poc.git'
            }
        }

        stage('Build MsSQL Image') {
            steps {
                sh 'cd mssql && docker build -t jhidalgoc/mssql:latest .'
            }
        }

        stage('Build Demo Image') {
            steps {
                sh 'cd app && docker build -t jhidalgoc/poc-udd:latest .'
            }
        }

        stage('Push MsSQL Image') {
            steps {
                sh 'echo $DOCKER_PASS | docker login --username $DOCKER_USER --password-stdin'
                sh 'docker push jhidalgoc/mssql:latest'
            }
        }

        stage('Push Demo Image') {
            steps {
                sh 'echo $DOCKER_PASS | docker login --username $DOCKER_USER --password-stdin'
                sh 'docker push jhidalgoc/poc-udd:latest'
            }
        }

        stage('Create NameSpace') {
            steps {
                sh 'kubectl delete --ignore-not-found=true -f ns.yaml && kubectl apply -f ns.yaml'
            }
        }

        stage('Apply MsSQL Deployment') {
            steps {
                sh 'cd mssql && kubectl apply -f deploy.yaml'
            }
        }

        stage('Create DB And Tables') {
            steps {
                sh 'while [[ $(kubectl get pods -n poc -l app=movies-bd -o "jsonpath={..status.conditions[?(@.type==\\"Ready\\")].status}") != True ]]; do echo "waiting for pod" && sleep 1; done'
            }
        }

        stage('Apply Demo Deployment') {
            steps {
                sh 'cd app && kubectl apply -f serviceaccount.yaml'
                sh 'cd app && kubectl apply -f service.yaml'
                sh 'cd app && kubectl apply -f deploy-v1.yaml'
                sh 'cd app && kubectl apply -f deploy-v2.yaml'
                sh 'cd app && kubectl apply -f ingress.yaml'
            }
        }

    }
}
