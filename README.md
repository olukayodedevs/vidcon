
# 🎥 Video Converter Microservices DevOps Project 🎧

In this project, I built and deployed a **video-to-audio converter** using a **microservices architecture**. The goal was to automate video conversion (MP4 to MP3) using modern **DevOps** practices, with a focus on **containerization**, **orchestration**, and **automation** using **Kubernetes** and **AWS EKS**.

![Project Architecture](./public/assets/image.png)

---

## 🛠️ Tech Stack

This project utilized various tools and technologies to ensure a scalable and secure deployment:

- **Kubernetes (EKS)**: Orchestration of microservices.
- **Docker**: Containerization of the services.
- **Helm**: Kubernetes package management.
- **AWS EKS**: Managed Kubernetes cluster on AWS.
- **MongoDB & PostgreSQL**: Databases for video metadata and user data.
- **RabbitMQ**: Message queuing for task management.
- **Python**: Backend services for handling business logic.
- **kubectl & AWS CLI**: Management and deployment tools for Kubernetes and AWS.

---

## 📐 Project Architecture

The application consists of multiple microservices, each responsible for a specific task. This microservices-based design allows for scalability, better resource management, and easier updates.

### Microservices:

- **Auth Service**: Handles user authentication and JWT token generation.
- **Converter Service**: Converts MP4 videos to MP3 audio files.
- **Notification Service**: Sends notifications to users upon successful conversion.
- **Database Service**: Manages user data (PostgreSQL) and video metadata (MongoDB).
- **RabbitMQ**: Manages queues for MP4 and MP3 conversion jobs.

Each service runs in its own Docker container, and communication between services is handled through RabbitMQ.

---

## 🌐 Endpoints

| Endpoint            | Description                                  | Method |
|---------------------|----------------------------------------------|--------|
| `/login`            | User login and JWT token generation          | POST   |
| `/upload`           | Upload MP4 video and start conversion process | POST   |
| `/download?fid=<ID>`| Download MP3 file using file ID               | GET    |

---

## 🛠️ Setup & Deployment

Here’s the step-by-step process I followed to deploy the microservices:

### 1. **AWS EKS Cluster Setup**  
   - Created the cluster using **AWS Console** and **eksctl**.
   - Provisioned the cluster with **t3.medium** instances for resource optimization.
   
   ![EKS Cluster](./public/assets/eks.png)

### 2. **Containerization with Docker**  
   - Each microservice was packaged in its own Docker container.
   - Pushed the images to DockerHub for easy deployment.  
   
   ![Docker](./public/assets/docker.png)
   *Containers pushed to DockerHub for deployment.*

### 3. **Database Deployment**  
   - Used **Helm** to deploy **MongoDB** and **PostgreSQL** for video metadata and user authentication.
   
   ```bash
   # Deploy MongoDB
   helm install mongo ./Helm_charts/MongoDB

   # Deploy PostgreSQL
   helm install postgres ./Helm_charts/Postgres
   ```

### 4. **RabbitMQ Setup**  
   - Deployed RabbitMQ for managing message queues.
   - Created queues for **MP4** and **MP3** conversion jobs.

   ![RabbitMQ Queues](./public/assets/rabbitmq.png)

### 5. **Microservice Deployment**  
   - Applied the manifest files for each service in the **Kubernetes cluster**.

   ```bash
   # Auth Service Deployment
   kubectl apply -f auth-service/manifest

   # Converter Service Deployment
   kubectl apply -f converter-service/manifest
   ```

   ![Microservices Deployed](./public/assets/microservices.png)

### 6. **Monitoring and Alerts**  
   - **Prometheus** and **Grafana** were deployed to monitor the health of the Kubernetes cluster.
   
   ![Grafana Dashboard](./public/assets/grafana.png)  
   *Grafana visualizing system metrics from Prometheus.*

### 7. **ArgoCD for GitOps**  
   - Integrated **ArgoCD** for continuous deployment, ensuring that all updates to the codebase were reflected in the cluster automatically.

   ![ArgoCD Sync](./public/assets/argocd.png)

---

## 📊 Application Flow

1. **User Authentication**: Users authenticate and receive a JWT token.
2. **Video Upload**: MP4 files are uploaded via the `/upload` endpoint.
3. **Queue Management**: RabbitMQ queues the file for conversion.
4. **Conversion Process**: The converter service converts MP4 to MP3.
5. **Notification**: Users are notified via email after the conversion is complete.
6. **File Download**: The converted MP3 file is available for download.

---

## 📈 Monitoring & Metrics

Using **Prometheus** and **Grafana**, I monitored the Kubernetes cluster, checking for CPU usage, memory consumption, and system health. This ensured the application was running smoothly without over-utilizing resources.

![Prometheus Targets](./public/assets/Prometheus.png)
*Prometheus scraping metrics from the microservices.*

---

## 📦 Clean-Up

Once the microservices had been validated and tested, I destroyed the infrastructure:

1. Deleted the **Node Groups** in the EKS cluster.
2. Deleted the **EKS cluster** and removed all associated resources.

```bash
# Delete the Node Group
aws eks delete-nodegroup --cluster-name <cluster_name> --nodegroup-name <node_group_name>

# Delete the EKS Cluster
aws eks delete-cluster --name <cluster_name>
```

---

## 🚀 Conclusion

This project gave me valuable experience in deploying **microservices** on AWS, managing **containers** with **Docker**, orchestrating services using **Kubernetes**, and automating CI/CD pipelines with **Helm** and **ArgoCD**. Additionally, integrating **Prometheus** and **Grafana** for monitoring helped me ensure the stability and performance of the system.



