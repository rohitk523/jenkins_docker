# jenkins_docker

# Installing Jenkins with Docker

![Jenkins Logo](https://jenkins.io/images/logos/jenkins/jenkins.png)

## Overview
This repository contains a guide for installing and running Jenkins using Docker. Jenkins is a powerful open-source automation server that enables developers to build, test, and deploy their software reliably.

## Prerequisites
- Docker installed on your system ([Get Docker](https://docs.docker.com/get-docker/))
- Basic knowledge of Docker commands
- Sudo/admin privileges

## Quick Start

### 1. Pull the Jenkins Image
```bash
docker pull jenkins/jenkins:lts
```

### 2. Create a Jenkins Home Directory
```bash
mkdir -p jenkins_home
```

### 3. Run Jenkins Container
```bash
docker run --name jenkins-server --restart=on-failure \
  -p 8080:8080 -p 50000:50000 \
  -v $(pwd)/jenkins_home:/var/jenkins_home \
  -d jenkins/jenkins:lts
```

### 4. Access Jenkins
- Open your browser and navigate to [http://localhost:8080](http://localhost:8080)
- Get the initial admin password:
  ```bash
  docker exec jenkins-server cat /var/jenkins_home/secrets/initialAdminPassword
  ```

## Advanced Configuration

### Persist Jenkins Data
The command above maps a local directory to `/var/jenkins_home` in the container, ensuring your Jenkins data persists between container restarts.

### Custom Jenkins Configuration
You can customize your Jenkins installation by mounting a custom `jenkins.yaml` file:

```bash
docker run --name jenkins-server \
  -p 8080:8080 -p 50000:50000 \
  -v $(pwd)/jenkins_home:/var/jenkins_home \
  -v $(pwd)/jenkins.yaml:/var/jenkins_home/jenkins.yaml \
  -d jenkins/jenkins:lts
```

### Using Docker-Compose
For a more maintainable setup, use docker-compose:

```yaml
version: '3'
services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins-server
    restart: unless-stopped
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - ./jenkins_home:/var/jenkins_home
```

Save this as `docker-compose.yml` and run:
```bash
docker-compose up -d
```

## Troubleshooting

### Permission Issues
If you encounter permission issues, adjust the ownership of your jenkins_home directory:

```bash
sudo chown 1000:1000 -R jenkins_home
```

### Container Won't Start
Check the logs:
```bash
docker logs jenkins-server
```

## Security Considerations
- Change the default admin password immediately
- Set up proper user accounts with appropriate permissions
- Use HTTPS when exposing Jenkins to the public
- Regularly update Jenkins and its plugins

## Maintenance

### Updating Jenkins
```bash
docker pull jenkins/jenkins:lts
docker stop jenkins-server
docker rm jenkins-server
# Then run the container again with the same parameters
```

### Backup
Backup your Jenkins data regularly:
```bash
tar -czvf jenkins_backup.tar.gz jenkins_home
```

## Contributing
Feel free to submit issues or pull requests to improve this guide!

## License
This project is licensed under the MIT License - see the LICENSE file for details.
