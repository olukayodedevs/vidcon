
# Video Converter Microservices Project

## Overview

I recently completed a **DevOps project** that focused on building and deploying a microservices-based application to convert **MP4 videos to MP3 audio files**. The project was deployed on **AWS Elastic Kubernetes Service (EKS)**, utilizing several microservices for authentication, video conversion, and notification delivery. It leveraged **RabbitMQ** for message queuing and **PostgreSQL** and **MongoDB** for database management.

The project gave me hands-on experience with cloud-based microservices architecture and Kubernetes. Here’s a detailed breakdown of how I approached and deployed the project.

---

## Architecture

The application was designed using a microservices architecture with the following components:

- **Auth Service**: Handles user login and authentication using JWT tokens.
- **Converter Module**: Converts video files (MP4) into audio files (MP3).
- **Database Server**: Uses PostgreSQL to store user data and MongoDB to store file metadata.
- **Notification Service**: Sends an email notification to users after a successful conversion.

RabbitMQ was used to manage queues between the video and MP3 conversion process.

---

## Project Workflow

### Step 1: Setting Up AWS EKS

The first step was to set up the infrastructure. I used **AWS EKS** to deploy the Kubernetes cluster. This involved:

1. **Creating IAM roles** for both the EKS cluster and nodes.
2. **Provisioning the EKS cluster** via the AWS Console and configuring VPC and subnets.
3. **Adding a node group** to ensure the cluster had sufficient compute resources.

```bash
aws eks update-kubeconfig --name <cluster_name> --region <aws_region>
```

### Step 2: Deploying Databases (PostgreSQL & MongoDB)

For data storage, I deployed two different databases:

- **PostgreSQL**: Handles user authentication data.
- **MongoDB**: Stores video and audio file metadata.

I used **Helm** to deploy both databases:

```bash
# Install PostgreSQL
cd Helm_charts/Postgres
helm install postgres .

# Install MongoDB
cd Helm_charts/MongoDB
helm install mongo .
```

---

### Step 3: Deploying RabbitMQ for Queue Management

Next, I deployed **RabbitMQ** to manage the messaging queues between the video and MP3 conversion process. I created two queues (`mp3` and `video`) to handle different stages of conversion.

```bash
helm install rabbitmq .
```

I verified that RabbitMQ was running and the queues were properly set up.

---

### Step 4: Deploying Microservices

With the infrastructure ready, I deployed the core microservices:

1. **Auth Service**: Manages user login and issues JWT tokens.
   ```bash
   kubectl apply -f auth-service/manifest
   ```

2. **Converter Module**: Performs the actual video-to-audio conversion.
   ```bash
   kubectl apply -f converter-service/manifest
   ```

3. **Notification Service**: Sends email notifications after conversions.
   ```bash
   kubectl apply -f notification-service/manifest
   ```

4. **Gateway Service**: Acts as a gateway for routing API requests.
   ```bash
   kubectl apply -f gateway-service/manifest
   ```

---

### Step 5: Configuring Notification Service with 2FA

To ensure secure email notifications, I implemented **2-factor authentication** (2FA) using Gmail for sending out notifications. I generated an **application-specific password** from Gmail and stored it in the Kubernetes secrets for the notification service.

---

### Step 6: Testing the API Endpoints

Once the microservices were up, I tested the API endpoints to make sure everything was functioning correctly.

- **Login Endpoint**:
  ```bash
  curl -X POST http://<nodeIP>:30002/login -u <email>:<password>
  ```

- **Upload Video (MP4)**:
  ```bash
  curl -X POST -F 'file=@./video.mp4' -H 'Authorization: Bearer <JWT Token>' http://<nodeIP>:30002/upload
  ```

- **Download MP3**:
  After conversion, I verified the MP3 file could be downloaded using:
  ```bash
  curl --output video.mp3 -X GET -H 'Authorization: Bearer <JWT Token>' "http://<nodeIP>:30002/download?fid=<Generated_fid>"
  ```

---

## Application Workflow Diagram

Here’s an overview of the application's architecture:

<p align="center">
  <img src="./Project documentation/ProjectArchitecture.png" width="600" title="Architecture" alt="Architecture">
  </p>

---

## Conclusion

Through this project, I gained practical experience in deploying a full-fledged microservices application on **AWS EKS**, integrating **RabbitMQ**, and managing data with **PostgreSQL** and **MongoDB**. I also enhanced my skills in Kubernetes and secured API services using JWT tokens. This project allowed me to build a complete end-to-end DevOps solution, emphasizing scalability, automation, and security.

Feel free to check out the code and ask any questions!

---

## Contact

If you would like to connect or have questions, feel free to reach out via my [LinkedIn](https://linkedin.com/in/arindeolukayode).
