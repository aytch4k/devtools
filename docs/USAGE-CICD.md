# CI/CD Integration and Troubleshooting

This section covers CI/CD integration and troubleshooting for the development environment.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](#ci-cd-integration) (this file)

## CI/CD Integration

### Using the Jenkinsfile

The included Jenkinsfile automates:
- Building the Docker images
- Creating necessary volumes
- Deploying the development environment
- Running smoke tests

To use it:

1. Configure Jenkins with necessary credentials:
   - `docker-registry-url`: URL of your Docker registry
   - `docker-registry-credentials`: Username/password for Docker registry

2. Create a Jenkins pipeline job pointing to the Jenkinsfile

3. Run the pipeline to build and deploy the environment

### Manual Build and Push

```bash
cd devtools/docker
docker build -t your-registry/blockchain-dev-env:latest .
docker push your-registry/blockchain-dev-env:latest
```

### GitHub Actions Integration

You can also use GitHub Actions to automate the build and deployment process. Here's an example workflow:

```yaml
# .github/workflows/build-deploy.yml
name: Build and Deploy

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_HUB_USERNAME }}
          password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}
      
      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: ./devtools/docker
          push: true
          tags: yourusername/blockchain-dev-env:latest
      
      - name: Deploy to development environment
        if: github.ref == 'refs/heads/main'
        run: |
          echo "Deploying to development environment..."
          # Add deployment commands here
```

### GitLab CI/CD Integration

Here's an example GitLab CI/CD configuration:

```yaml
# .gitlab-ci.yml
stages:
  - build
  - test
  - deploy

variables:
  DOCKER_IMAGE: $CI_REGISTRY_IMAGE/blockchain-dev-env:$CI_COMMIT_REF_SLUG

build:
  stage: build
  image: docker:20.10.16
  services:
    - docker:20.10.16-dind
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $DOCKER_IMAGE ./devtools/docker
    - docker push $DOCKER_IMAGE

test:
  stage: test
  image: $DOCKER_IMAGE
  script:
    - echo "Running tests..."
    # Add test commands here

deploy:
  stage: deploy
  image: docker:20.10.16
  services:
    - docker:20.10.16-dind
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker pull $DOCKER_IMAGE
    - echo "Deploying to environment..."
    # Add deployment commands here
  only:
    - main
```

## Troubleshooting

### Common Issues

1. **Container fails to start**
   ```bash
   # Check logs
   docker-compose logs dev-environment
   
   # Increase memory limit
   # Edit docker-compose.yml to add:
   services:
     dev-environment:
       deploy:
         resources:
           limits:
             memory: 8G
   ```

2. **Blockchain nodes not connecting**
   ```bash
   # Check if nodes are running
   docker-compose ps
   
   # Restart specific node
   docker-compose restart geth
   ```

3. **Port conflicts**
   ```bash
   # Edit docker-compose.yml to change port mappings
   # For example, change "3000:3000" to "3002:3000"
   ```

4. **IPFS not working**
   ```bash
   # Initialize IPFS manually
   docker-compose exec dev-environment ipfs init
   
   # Start IPFS daemon
   docker-compose exec -d dev-environment ipfs daemon
   ```

5. **Database connection issues**
   ```bash
   # Check if database containers are running
   docker-compose ps mongodb postgres neo4j milvus
   
   # Check database logs
   docker-compose logs mongodb
   
   # Verify connection from dev container
   docker-compose exec dev-environment connect-postgres
   docker-compose exec dev-environment connect-mongodb
   docker-compose exec dev-environment connect-neo4j
   docker-compose exec dev-environment connect-milvus
   ```

6. **Out of disk space**
   ```bash
   # Clean up unused Docker resources
   docker system prune -a
   
   # Remove unused volumes
   docker volume prune
   ```

7. **GPU acceleration not working**
   ```bash
   # Check if GPU is available
   docker-compose exec dev-environment nvidia-smi
   
   # Update docker-compose.yml to use GPU
   services:
     dev-environment:
       deploy:
         resources:
           reservations:
             devices:
               - driver: nvidia
                 count: 1
                 capabilities: [gpu]
   ```

### Debugging Tools

1. **Inspect container**
   ```bash
   # Enter container shell
   docker-compose exec dev-environment bash
   
   # Check environment variables
   env | grep -i eth
   
   # Check running processes
   ps aux
   ```

2. **Network debugging**
   ```bash
   # Check network connectivity
   docker-compose exec dev-environment ping geth
   
   # Check open ports
   docker-compose exec dev-environment netstat -tulpn
   
   # Check DNS resolution
   docker-compose exec dev-environment nslookup mongodb
   ```

3. **Resource monitoring**
   ```bash
   # Check container stats
   docker stats
   
   # Check disk usage
   docker-compose exec dev-environment df -h
   
   # Check memory usage
   docker-compose exec dev-environment free -m
   ```

### Getting Help

If you encounter issues not covered here:

1. Check container logs: `docker-compose logs`
2. Inspect running containers: `docker-compose ps`
3. Enter the container for debugging: `docker-compose exec dev-environment bash`
4. Check the GitHub repository issues section for similar problems
5. Consult the documentation for specific tools or frameworks

## Maintenance

### Updating the Environment

```bash
# Pull latest changes
git pull

# Rebuild the Docker image
cd devtools/docker
docker-compose build --no-cache

# Restart the environment
docker-compose down
docker-compose up -d
```

### Backing Up Data

```bash
# Backup volumes
docker run --rm -v blockchain-dev-env_postgres-data:/source -v $(pwd):/backup alpine tar -czf /backup/postgres-data.tar.gz -C /source .
docker run --rm -v blockchain-dev-env_mongodb-data:/source -v $(pwd):/backup alpine tar -czf /backup/mongodb-data.tar.gz -C /source .
docker run --rm -v blockchain-dev-env_neo4j-data:/source -v $(pwd):/backup alpine tar -czf /backup/neo4j-data.tar.gz -C /source .

# Restore volumes
docker run --rm -v blockchain-dev-env_postgres-data:/target -v $(pwd):/backup alpine sh -c "rm -rf /target/* && tar -xzf /backup/postgres-data.tar.gz -C /target"
docker run --rm -v blockchain-dev-env_mongodb-data:/target -v $(pwd):/backup alpine sh -c "rm -rf /target/* && tar -xzf /backup/mongodb-data.tar.gz -C /target"
docker run --rm -v blockchain-dev-env_neo4j-data:/target -v $(pwd):/backup alpine sh -c "rm -rf /target/* && tar -xzf /backup/neo4j-data.tar.gz -C /target"
```

### Security Considerations

1. **Container security**
   - Keep Docker and container images updated
   - Use minimal base images
   - Scan images for vulnerabilities: `docker scan blockchain-dev-env:latest`

2. **Network security**
   - Use internal Docker networks for communication between services
   - Expose only necessary ports
   - Use TLS for external connections

3. **Data security**
   - Use volume mounts for persistent data
   - Implement regular backups
   - Use secrets management for sensitive information

4. **Access control**
   - Implement proper authentication and authorization
   - Use least privilege principle
   - Rotate credentials regularly