# FinacPlus DevOps CI/CD Case Study

A hands-on project demonstrating a complete CI/CD pipeline for a Node.js application using Jenkins, Docker, Kubernetes, and Helm. Monitoring is handled with Prometheus and Grafana.

📄 **Detailed Documentation:** [FinacPlus_DevOps_Solution](https://xyz.com](https://docs.google.com/document/d/19mZPL6CoarPg_VznXe5osa4jiEbzmUv2DAGUMveJNQM/edit?usp=sharing))  



## Tech Stack

- **Node.js** – Application framework  
- **Jenkins** – CI/CD automation  
- **Docker** – Containerization  
- **Helm** – Kubernetes packaging  
- **Kubernetes** – Application deployment  
- **Prometheus + Grafana** – Monitoring and dashboards  
- **GitHub + Ngrok** – Webhook integration for local testing  


## What’s Included

- GitHub integration with Jenkins using webhook (via Ngrok for local)
- Secure credential handling with Jenkins global credentials
- CI/CD pipeline steps:  
  `Checkout → Install Dependencies → Build → Test → Dockerize → Push Image → Helm Deploy → K8s`
- Helm chart dynamically updated with Docker image and tag
- Live app monitoring setup with Prometheus and Grafana dashboards


## Folder Structure

- `application/` – Node.js frontend  
- `helm/` – Helm chart setup  
- `monitoring/` – Prometheus & Grafana config  
- `Jenkinsfile` – Full pipeline configuration  


## Also Built...

A similar end-to-end CI/CD pipeline for a **Java application** one year ago using tools like GitHub, Jenkins, Maven, SonarQube, Nexus, Docker, Trivy, Kubernetes, and monitoring/alerting.

📌 **Check it out:** [JENKINS CI/CD](https://hemanthgangula.hashnode.dev/efficient-end-to-end-cicd)
