# SonarQube Installation using Docker on Ubuntu 22.04

## Prerequisites

Ensure you have the following before proceeding with the installation:

- A fresh Ubuntu 22.04 EC2 instance with at least t2.medium (4GB RAM)

- Port 9000 opened in the security firewall rules

- Java JDK installed

## Step-by-Step Installation Guide

### 1. Update System Packages
```
sudo apt update
```
### 2. Set System Configuration Parameters
Modify the system configuration file:
```
sudo vi /etc/sysctl.conf
```
Add the following lines at the end of the file:
```
vm.max_map_count=262144
fs.file-max=65536
```
Apply the changes:
```
sudo sysctl -p
```

### 3. Change Hostname to SonarQube
```
sudo hostnamectl set-hostname SonarQube
```
### 4 . Install Docker and Docker-Compose
```
sudo apt install docker-compose -y
```
### 5. Create docker-compose.yml File
Create a new file:
```
vi docker-compose.yml
```
Paste the following configuration:
```
version: "3"
services:
  sonarqube:
    image: sonarqube:community
    restart: unless-stopped
    depends_on:
      - db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/sonarqube/data
      - sonarqube_extensions:/sonarqube/extensions
      - sonarqube_logs:/sonarqube/logs
    ports:
      - "9000:9000"
    networks:
      - sonarqube-net

  db:
    image: postgres:12
    restart: unless-stopped
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data
    networks:
      - sonarqube-net

networks:
  sonarqube-net:

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
```

### 6. Deploy SonarQube Using Docker Compose
```
sudo docker-compose up -d
```
### 7. Verify SonarQube is Running
```
sudo docker-compose logs --follow
```
### 8. Access SonarQube Dashboard
```
http://<your-ec2-instance-ip>:9000
```
### 9. Default Credentials

- Username: admin

- Password: admin
