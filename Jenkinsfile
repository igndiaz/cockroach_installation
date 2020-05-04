pipeline {
    agent any 
    parameters {
        string(name: 'NODO1', defaultValue: 'localhost', description: 'IP Nodo1')
        string(name: 'NODO2', defaultValue: 'localhost', description: 'IP Nodo2')
        string(name: 'NODO3', defaultValue: 'localhost', description: 'IP Nodo3')
    }
    stages {
        stage('Instalacion CockroachDB') {
            steps {
                sh "wget -qO- https://binaries.cockroachdb.com/cockroach-v19.2.6.linux-amd64.tgz | tar  xvz"
                sh "sudo cp -i cockroach-v19.2.6.linux-amd64/cockroach /usr/local/bin/"
            }  
        }
        stage('Inicio Nodos') {
            steps {
                sh "cockroach start --insecure --listen-addr=${params.Nodo1}:26257 --join=${params.Nodo1}:26257,${params.Nodo2}:26258,${params.Nodo3}:26259 --http-addr=localhost:8080 --store=cockroach-data-1 --background"
                sh "cockroach start --insecure --listen-addr=${params.Nodo2}:26258 --join=${params.Nodo1}:26257,${params.Nodo2}:26258,${params.Nodo3}:26259 --http-addr=localhost:8081 --store=cockroach-data-2 --background"
                sh "cockroach start --insecure --listen-addr=${params.Nodo3}:26259 --join=${params.Nodo1}:26257,${params.Nodo2}:26258,${params.Nodo3}:26259 --http-addr=localhost:8082 --store=cockroach-data-3 --background"
            }   
        }  
        stage('Inicio Cluster') {
            steps {
                sh "cockroach init --host ${params.Nodo1}:26257 --insecure"
                sh "sleep 30"
            }  
        }    
        stage('Validación Funcionamiento') {
            steps {
                sh "cockroach node status"
                sh "cockroach sql --insecure -e 'SHOW databases;'"
            }  
        }  
    }
}
