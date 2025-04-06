pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = credentials('docker-registry-url')
        DOCKER_CREDS = credentials('docker-registry-credentials')
        IMAGE_NAME = 'blockchain-dev-env'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                dir('devtools/docker') {
                    sh 'docker build -t ${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} .'
                    sh 'docker tag ${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest'
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                sh 'echo ${DOCKER_CREDS_PSW} | docker login ${DOCKER_REGISTRY} -u ${DOCKER_CREDS_USR} --password-stdin'
                sh 'docker push ${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}'
                sh 'docker push ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest'
            }
        }
        
        stage('Deploy Development Environment') {
            steps {
                dir('devtools/docker') {
                    // Create necessary volumes if they don't exist
                    sh '''
                    docker volume create --name=dev-node-modules || true
                    docker volume create --name=dev-go-pkg || true
                    docker volume create --name=dev-cargo-registry || true
                    
                    # Blockchain data volumes
                    docker volume create --name=geth-data || true
                    docker volume create --name=solana-data || true
                    docker volume create --name=cosmos-data || true
                    docker volume create --name=polkadot-data || true
                    docker volume create --name=algorand-data || true
                    docker volume create --name=cardano-data || true
                    docker volume create --name=tezos-data || true
                    docker volume create --name=avalanche-data || true
                    docker volume create --name=near-data || true
                    
                    # Layer 2 data volumes
                    docker volume create --name=zksync-data || true
                    docker volume create --name=optimism-data || true
                    docker volume create --name=arbitrum-data || true
                    docker volume create --name=starknet-data || true
                    docker volume create --name=polygon-zkevm-data || true
                    
                    # Cross-chain data volumes
                    docker volume create --name=ibc-relayer-data || true
                    docker volume create --name=axelar-data || true
                    docker volume create --name=wormhole-data || true
                    docker volume create --name=layerzero-data || true
                    
                    # Storage data volumes
                    docker volume create --name=ipfs-data || true
                    docker volume create --name=ceramic-data || true
                    
                    # Database data volumes
                    docker volume create --name=postgres-data || true
                    docker volume create --name=mongodb-data || true
                    docker volume create --name=mongodb-config || true
                    docker volume create --name=neo4j-data || true
                    docker volume create --name=neo4j-logs || true
                    docker volume create --name=neo4j-plugins || true
                    docker volume create --name=milvus-data || true
                    docker volume create --name=etcd-data || true
                    docker volume create --name=minio-data || true
                    '''
                    
                    // Pull the latest image and deploy
                    sh '''
                    docker-compose pull
                    docker-compose up -d
                    '''
                }
            }
        }
        
        stage('Run Smoke Tests') {
            steps {
                // Wait for services to be ready
                sh 'sleep 30'
                
                // Test if main container is running
                sh 'docker-compose -f devtools/docker/docker-compose.yml ps dev-environment | grep "Up"'
                
                // Test if blockchain nodes are running
                sh 'docker-compose -f devtools/docker/docker-compose.yml ps geth | grep "Up"'
                sh 'docker-compose -f devtools/docker/docker-compose.yml ps solana | grep "Up"'
                
                // Test if database services are running
                sh 'docker-compose -f devtools/docker/docker-compose.yml ps postgres | grep "Up"'
                sh 'docker-compose -f devtools/docker/docker-compose.yml ps mongodb | grep "Up"'
                
                // Test connectivity to services
                sh 'docker-compose -f devtools/docker/docker-compose.yml exec -T dev-environment curl -s http://geth:8545 -X POST -H "Content-Type: application/json" --data \'{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}\''
                sh 'docker-compose -f devtools/docker/docker-compose.yml exec -T dev-environment curl -s http://ipfs:5001/api/v0/version'
            }
        }
    }
    
    post {
        always {
            // Clean up
            sh 'docker image prune -f'
        }
        
        success {
            echo 'Blockchain development environment deployed successfully!'
        }
        
        failure {
            echo 'Failed to deploy blockchain development environment'
            
            // Stop and remove containers on failure
            sh 'docker-compose -f devtools/docker/docker-compose.yml down || true'
        }
    }
}