FitFlow
FitFlow is my diploma project for the DevOps course. It is a web application for trainers and clients to manage training programs, schedules, and attendance. My part of the project was setting up the servers, deploying the application, and monitoring it.
How I set it up
I used Terraform to create two Linux servers in Azure: one for FitFlow and one for Jenkins. I also used three local virtual machines. Two run PostgreSQL, one as the primary database and the other as its replica. The third runs monitoring and the K3s control plane. WireGuard connects the Azure and local servers over a private network.

I used Ansible to configure the servers. For example, I used it to install Docker, set up firewall rules, and install Node Exporter on all five machines.
How I deploy the app
The frontend runs on Nginx, the API is written in Python with FastAPI, and a Celery worker uses Redis for background jobs. The application stores its data in PostgreSQL.

I set up two ways to run FitFlow on the Azure application server: Docker Compose and Kubernetes (K3s). The Compose frontend uses port 80. The Kubernetes frontend uses port 30080.

When I push changes to the develop branch, GitHub sends a webhook to Jenkins. Jenkins builds the frontend, API, and worker images and pushes them to Docker Hub. Each build has its own tag so I can tell which version is running. The repository has a Compose pipeline and a [K3s pipeline](Jenkinsfile.k3s). Both check that the application responds after deployment.
How I tested monitoring
Prometheus collects metrics from all five servers, and I use Grafana to see CPU, memory, and disk usage.

To test alerts, I stopped Node Exporter on the Jenkins server. Jenkins kept running, but Prometheus could no longer collect metrics from that server. After two minutes, the alert fired and Alertmanager sent me a message on Telegram. When I started Node Exporter again, I received a recovery message.