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
                sh 'while [[ $(kubectl get pods -n poc -l app=mssql -o "jsonpath={..status.conditions[?(@.type==\"Ready\")].status}") != "True" ]]; do echo "waiting for pod" && sleep 1; done'
                sh 'kubectl exec deployments/mssql -n poc -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P P4ssw0rd -Q "CREATE DATABASE demo ON (FILENAME=N\'/data(demo.mdf\'),  (FILENAME=N\'/data(demo.ldf\')"'
		sh 'kubectl exec deployments/mssql -n poc -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P P4ssw0rd -d demo -Q "Create Table Movie([ID]  INT Identity (1,1) NOT NULL,[Genre] nvarchar(MAX) null,[Price] decimal(18,2) not null,[Rating] nvarchar(MAX) null,[ReleaseDate] datetime2 (7) not null,[Title] nvarchar(MAX) NULL,Constraint [PK_Movie] Primary Key Clustered ([ID] Asc))"'
            }
        }
		
        stage('Apply Demo Deployment') {
            steps {
                sh 'cd app && kubectl apply -f deploy.yaml'
            }
        }
        
    }
}
