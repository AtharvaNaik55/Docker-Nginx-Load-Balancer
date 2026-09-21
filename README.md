# Docker-Nginx-Load-Balancer

A production-style **Docker Compose + Nginx Load Balancer** project that demonstrates how to distribute incoming HTTP traffic across multiple backend application containers.

The project runs **Nginx as a reverse proxy and load balancer** in front of three backend containers, all connected through a Docker network.

---

## 🏗️ Architecture

```text
                         🌐 INTERNET
                              │
                              │ HTTP :80
                              ▼
                    ┌─────────────────────┐
                    │      AWS EC2        │
                    │  Amazon Linux 2023  │
                    │                     │
                    │   ┌─────────────┐   │
                    │   │    NGINX    │   │
                    │   │    :80      │   │
                    │   │Load Balancer│   │
                    │   └──────┬──────┘   │
                    │          │          │
                    │    Docker Network   │
                    │          │          │
                    │    ┌─────┼─────┐    │
                    │    │     │     │    │
                    │    ▼     ▼     ▼    │
                    │ ┌────┐ ┌────┐ ┌────┐│
                    │ │ B1 │ │ B2 │ │ B3 ││
                    │ │5000│ │5000│ │5000││
                    │ └────┘ └────┘ └────┘│
                    └─────────────────────┘
Request Flow
Client
  │
  │ HTTP Request
  ▼
AWS EC2 :80
  │
  ▼
Nginx Load Balancer
  │
  ├──────► backend1:5000
  │
  ├──────► backend2:5000
  │
  └──────► backend3:5000
✨ Features
🐳 Docker containerization
⚙️ Docker Compose orchestration
🌐 Nginx reverse proxy
⚖️ Nginx round-robin load balancing
🔗 Docker internal networking
🐍 Python backend application
☁️ AWS EC2 deployment
🐧 Amazon Linux 2023
🔄 Automatic container restart
🔒 Backend containers are not publicly exposed
🛠️ Easy troubleshooting and scaling
🧰 Technologies
Technology	Purpose
🐳 Docker	Containerization
⚙️ Docker Compose	Manage multiple containers
🌐 Nginx	Reverse proxy and load balancer
🐍 Python	Backend application
☁️ AWS EC2	Cloud infrastructure
🐧 Amazon Linux 2023	Server operating system
🔧 Git	Version control
🐙 GitHub	Source code management
📁 Project Structure
Docker-Nginx-Load-Balancer/
│
├── README.md
├── docker-compose.yaml
├── .gitignore
│
├── nginx/
│   ├── Dockerfile
│   └── nginx.conf
│
├── architecture-diagram.png
│ 
│
├── screenshots/
│   ├── docker-compose-ps.png
│   ├── docker-images.png
│   ├── docker-ps.png
│   └── nginx-test.png

🐳 Docker Services
Container	Internal Port	Purpose
nginx-lb	80	Load balancer / reverse proxy
backend1	5000	Backend application
backend2	5000	Backend application
backend3	5000	Backend application

Only Nginx exposes a port to the host:

Host :80 → Nginx :80

The backend containers communicate internally through Docker networking.

⚖️ Load Balancing

Nginx is configured with three backend servers:

upstream backend {
    server backend1:5000;
    server backend2:5000;
    server backend3:5000;
}

Nginx uses round-robin load balancing by default.

The request distribution concept is:

Request 1 → backend1
Request 2 → backend2
Request 3 → backend3
Request 4 → backend1
Request 5 → backend2
Request 6 → backend3

This allows incoming requests to be distributed across the available backend containers.

🌐 Nginx Configuration

The Nginx reverse proxy forwards incoming traffic to the backend group.

server {
    listen 80;

    location / {
        proxy_pass http://backend;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

Nginx receives the request on port 80 and forwards it to one of the backend containers.

🔗 Docker Networking

Docker Compose provides an internal network where the services can communicate using their service names.

Nginx can reach:

backend1:5000
backend2:5000
backend3:5000

There is no requirement to expose the backend port 5000 directly to the Internet.

                    Docker Network
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
          backend1   backend2   backend3
            :5000      :5000      :5000
🚀 Installation
Clone the Repository
git clone https://github.com/AtharvaNaik55/Docker-Nginx-Load-Balancer.git
cd Docker-Nginx-Load-Balancer
Check Docker
docker --version
docker compose version

Check the Docker service:

sudo systemctl status docker

Start Docker if required:

sudo systemctl start docker

Enable Docker at boot:

sudo systemctl enable docker
▶️ Run the Project

Build and start all services:

docker compose up --build -d

Check running containers:

docker compose ps

Expected services:

nginx-lb
backend1
backend2
backend3
🔍 Verify the Application

Test from the EC2 server:

curl http://localhost

Or open the EC2 public IP in a browser:

http://YOUR_EC2_PUBLIC_IP
📋 Useful Commands
Show running containers
docker ps
Show all containers
docker ps -a
Show images
docker images
Show Compose services
docker compose ps
View logs
docker compose logs
Follow logs
docker compose logs -f
View Nginx logs
docker logs nginx-lb
Stop the application
docker compose down
Rebuild the application
docker compose up --build -d
🔧 Nginx Troubleshooting

Test the Nginx configuration:

docker exec nginx-lb nginx -t

Expected result:

syntax is ok
test is successful

Check the Nginx container:

docker ps

Check Nginx logs:

docker logs nginx-lb
🔌 Backend Connectivity Test

Enter the Nginx container:

docker exec -it nginx-lb sh

Test backend1:

wget -qO- http://backend1:5000

Test backend2:

wget -qO- http://backend2:5000

Test backend3:

wget -qO- http://backend3:5000

Exit:

exit
☁️ AWS EC2 Configuration

The EC2 Security Group should allow:

Protocol	Port	Purpose
TCP	22	SSH
TCP	80	HTTP

The backend port 5000 does not need to be publicly accessible.

🔄 Deployment Workflow
              GitHub
                 │
                 │ git clone
                 ▼
            AWS EC2 Server
                 │
                 ▼
           Docker Compose
                 │
        ┌────────┼────────┐
        ▼        ▼        ▼
      Nginx    Backend  Backend
                1        2
                 │
                 ▼
              Backend 3
📊 Container Architecture
┌─────────────────────────────────────────────┐
│                  AWS EC2                    │
│                                             │
│              Docker Engine                  │
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │           Docker Network              │  │
│  │                                       │  │
│  │  ┌─────────────┐                      │  │
│  │  │   Nginx     │ :80                 │  │
│  │  │ nginx-lb    │                      │  │
│  │  └──────┬──────┘                      │  │
│  │         │                              │  │
│  │    ┌────┼────────────┐                │  │
│  │    │    │            │                │  │
│  │    ▼    ▼            ▼                │  │
│  │ ┌─────┐┌─────┐    ┌─────┐            │  │
│  │ │ B1  ││ B2  │    │ B3  │            │  │
│  │ │5000 ││5000 │    │5000 │            │  │
│  │ └─────┘└─────┘    └─────┘            │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
🧠 What This Project Demonstrates

This project demonstrates practical knowledge of:

Docker
Docker images
Docker containers
Docker Compose
Docker networking
Container-to-container communication
Nginx reverse proxy
Nginx load balancing
Round-robin load balancing
Port mapping
AWS EC2
Amazon Linux 2023
Linux administration
Git
GitHub
Container troubleshooting
Buildx
🔮 Future Improvements


Atharva Naik
