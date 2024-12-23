# Flask and MySQL Two-Tier Application on AWS EC2 with Docker Volumes
This repository contains a two-tier application built using Flask (backend) and MySQL (database), deployed using Docker on an AWS EC2 instance. The application runs on port `5000` and uses Docker's bridge network for communication between containers. Additionally, a Docker volume is configured for the MySQL container to ensure data persistence in case of crashes or container restarts.

## Features

- **Flask**: Python-based lightweight web framework for application logic.
- **MySQL**: Relational database for storing application data.
- **Docker**: Containerized deployment for Flask and MySQL.
- **Bridge Network**: Secure and efficient communication between Flask and MySQL containers.
- **Docker Volume**: Persistent storage for MySQL data.
- **AWS EC2**: Host the application on a cloud instance.

---

## Deployment Instructions

### 1. Launch an EC2 Instance
- Log in to your AWS account and navigate to the EC2 dashboard.
- Launch an Ubuntu instance (recommended version: Ubuntu 22.04).
- Select a security group and add rules to allow:
  - **SSH**: Port `22` for accessing the instance.
  - **HTTP**: Port `5000` for accessing the application.

### 2. Connect to the EC2 Instance
Use SSH to connect to your EC2 instance:
```bash
ssh -i "your-key.pem" ubuntu@<your-ec2-public-ip>
```

### 3. Install Docker on EC2

Run the following commands to install Docker:

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
```

### 4. Clone the Repository
Clone your repository containing the Flask and MySQL application:

```bash

git clone https://github.com/chiragpuniyanii/two-tier-flask-application.git
cd two-tier-flask-application/
```

### 5. Create a Docker Network
Set up a Docker bridge network for communication between containers:

```bash
docker network create two-tier -d bridge
```

### 6. Create a Docker Volume for MySQL
Create a volume to persist MySQL data:

```bash
docker volume create mysql-data
```

### 7. Run the MySQL Container
Run the MySQL container with appropriate environment variables:

```bash
docker pull mysql
docker run -d --name mysql --network two-tier -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=chirag -e MYSQL_DATABASE=chirag mysql
```
  - -v mysql-data:/var/lib/mysql: Maps the mysql-data volume to the MySQL data directory inside the container.

### 7. Build and Run the Flask Container
Build the Flask container using the Dockerfile:

```bash
docker build -t two-tier-flask .
docker run -d -p 5000:5000 --network two-tier -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=chirag -e MYSQL_DB=chirag two-tier-flask:latest
```


### 8. Access the Application
Open your browser and navigate to http://<your-ec2-public-ip>:5000.
![image](https://github.com/user-attachments/assets/06761d6f-14bd-4cc7-bfde-7b5b8b542c90)




## Application Architecture
**Flask Container:**

  - Runs the backend logic and listens on port 5000.
  - Communicates with MySQL via the app-network.

**MySQL Container:**

  - Stores application data.
  - Connects with Flask using the bridge network.
  - Data is persisted in the mysql-data volume, ensuring no data loss in case of crashes or restarts.


## Prerequisites
  - AWS EC2 Instance: Ubuntu 22.04 or similar.
  - Docker Installed: Required for containerization.
  - MySQL Image: Pulled automatically by Docker.

## Troubleshooting
  - Docker Permission Error: Ensure the ubuntu user is added to the docker group.
  - Port Access: Verify that port 5000 is open in the AWS Security Group.
  - MySQL Volume Issue: Check if the volume is correctly attached and accessible.


## Future Enhancements
  - Add a frontend for the application.
  - Use docker-compose for simplified deployment.
  - Integrate monitoring and logging.

## Contributions
  - Contributions are welcome! Fork the repository and submit pull requests for improvements.

