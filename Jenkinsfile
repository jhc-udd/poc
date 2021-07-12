pipeline {
    agent {label 'docker'}

    environment {
        REGISTRY="k8s-previos.udd.net"
        PATH_IMAGE="k8s-previos.udd.net/nexus/repository/udd-previos-registry"
        PATH = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
        DOCKER_USER = credentials('user-registry')
        DOCKER_PASS = credentials('pass-registry')
    }

    stages {
        stage('Clone sources') {
            steps {
                git url: 'https://github.com/jhc-udd/poc.git'
            }
        }

        stage('Build MsSQL Image') {
            steps {
                sh 'cd mssql && docker build -t $PATH_IMAGE/mssql:latest .'
            }
        }

        stage('Build Demo Image') {
            steps {
                sh 'cd app && docker build -t $PATH_IMAGE/poc-udd:latest .'
            }
        }

        stage('Push MsSQL Image') {
            steps {
                sh 'echo $DOCKER_PASS | docker login $REGISTRY --username $DOCKER_USER --password-stdin'
                sh 'docker push $PATH_IMAGE/mssql:latest'
            }
        }

        stage('Push Demo Image') {
            steps {
                sh 'echo $DOCKER_PASS | docker login $REGISTRY --username $DOCKER_USER --password-stdin'
                sh 'docker push $PATH_IMAGE/poc-udd:latest'
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
