# DevOps Tools, Workflow and Architecture

## 1. What Is DevOps?

DevOps combines Development (Dev) and Operations (Ops) practices to automate software delivery, improve collaboration, increase deployment frequency, and maintain application reliability.

DevOps tools help teams manage source code, build and test applications, deploy releases, provision infrastructure, manage configurations, orchestrate containers, and monitor production systems.

---

## 2. DevOps Tools by Category

### 2.1 Source Code Management (SCM)

**Purpose:** Track code changes, collaborate with developers, and maintain source code history.

Tools:
- Git — Distributed version control system
- GitHub — Hosts Git repositories and supports collaboration
- GitLab — Source control and integrated DevOps platform
- Bitbucket — Git repository hosting and collaboration

Common workflow:

`Developer → Git → GitHub → CI/CD Pipeline`

### 2.2 Build Tools

**Purpose:** Compile source code, resolve dependencies, and package applications.

Tools:
- Maven — Java build and dependency management
- Gradle — Build automation for Java and other JVM projects
- Ant — Java build automation
- npm — JavaScript package and build tooling

Example workflow:

`Source Code → Build Tool → Application Artifact`

### 2.3 Testing Tools

**Purpose:** Verify application correctness and prevent defective releases.

Tools:
- JUnit — Java unit testing
- pytest — Python testing
- Selenium — Browser automation testing
- Postman — API testing
- SonarQube — Static code analysis and code quality checks

Common testing stages:
- Unit testing
- Integration testing
- API testing
- Code quality analysis
- Security scanning

### 2.4 CI/CD Tools

**Purpose:** Automate the process of building, testing, and deploying applications.

Tools:
- Jenkins — Automation server for CI/CD pipelines
- GitHub Actions — Workflow automation integrated with GitHub
- GitLab CI/CD — Pipeline automation within GitLab
- Azure Pipelines — CI/CD service

Typical pipeline:

`Code Push → Build → Test → Scan → Package → Deploy`

### 2.5 Artifact Management

**Purpose:** Store and distribute versioned build outputs and packages.

Tools:
- JFrog Artifactory
- Sonatype Nexus Repository
- Amazon S3 for suitable artifact-storage workflows

Examples of artifacts:
- JAR files
- WAR files
- Python packages
- Container images

### 2.6 Containerization

**Purpose:** Package an application and its dependencies into a portable, isolated environment.

Tools:
- Docker — Builds and runs containers
- Podman — Builds and runs containers without a central daemon
- Buildah — Builds OCI-compatible container images

Important concepts:
- Dockerfile
- Container image
- Container
- Container registry
- Volumes
- Container networking

Common workflow:

`Source Code → Dockerfile → Container Image → Registry`

Registries include:
- Docker Hub
- Amazon ECR
- GitHub Container Registry
- Harbor

### 2.7 Container Orchestration

**Purpose:** Manage containers across a cluster, including scheduling, scaling, service discovery, and recovery.

Tools:
- Kubernetes — Container orchestration platform
- Helm — Packages and manages Kubernetes applications
- Argo CD — GitOps-based continuous delivery for Kubernetes
- Kustomize — Customizes Kubernetes manifests
- Docker Swarm — Docker container orchestration

Kubernetes responsibilities:
- Schedule workloads
- Maintain desired replica counts
- Restart failed containers
- Support rolling updates
- Enable service discovery
- Scale applications

### 2.8 Infrastructure as Code (IaC)

**Purpose:** Define and provision infrastructure using version-controlled configuration.

Tools:
- Terraform — Provisions and manages infrastructure
- OpenTofu — Open-source infrastructure as code tool
- AWS CloudFormation — Provisions AWS infrastructure
- Pulumi — Defines infrastructure using programming languages

Example resources:
- VPCs and subnets
- Virtual machines
- Load balancers
- Security groups
- Databases
- Kubernetes clusters

Workflow:

`IaC Code → Plan → Review → Apply → Infrastructure`

### 2.9 Configuration Management

**Purpose:** Automate server configuration and maintain consistent system states.

Tools:
- Ansible — Agentless automation and configuration management
- Puppet — Configuration management
- Chef — Infrastructure configuration automation
- Salt — Remote execution and configuration management

Ansible use cases:
- Install packages
- Create users
- Configure web servers
- Manage services
- Deploy application files
- Configure multiple servers consistently

### 2.10 Monitoring and Observability

**Purpose:** Understand system health, detect failures, and troubleshoot performance problems.

Tools:
- Prometheus — Collects and stores time-series metrics
- Grafana — Visualizes metrics and other observability data
- Alertmanager — Routes and groups Prometheus alerts
- Loki — Aggregates logs
- OpenTelemetry — Collects and exports telemetry
- Jaeger — Distributed tracing
- Elasticsearch, Logstash and Kibana (ELK) — Log processing, storage and visualization

Important concepts:
- Metrics
- Logs
- Traces
- Dashboards
- Alerts
- Service Level Indicators (SLIs)
- Service Level Objectives (SLOs)

---

## 3. End-to-End DevOps Workflow

```text
Developer
    |
    v
Git / GitHub
    |
    v
CI/CD Pipeline
(Jenkins / GitHub Actions)
    |
    v
Build Application
(Maven / Gradle / npm)
    |
    v
Automated Testing
(JUnit / pytest / Selenium)
    |
    v
Code Quality and Security Checks
(SonarQube / Security Scanners)
    |
    v
Package Application
(JAR / WAR / Other Artifact)
    |
    v
Build Container Image
(Docker / Podman)
    |
    v
Push Image to Registry
(ECR / Docker Hub / GHCR)
    |
    v
Deploy Application
(Kubernetes / Helm / Argo CD)
    |
    v
Application Running in Production
    |
    v
Monitor and Alert
(Prometheus / Grafana / Loki)
    |
    v
Troubleshoot and Improve
```

**Important:** Terraform and Ansible support this workflow by provisioning infrastructure and configuring systems. They are not necessarily sequential stages inside every application pipeline.

---

## 4. How Terraform and Ansible Fit Together

Terraform and Ansible solve different problems.

### Terraform — Infrastructure Provisioning

Terraform creates and manages infrastructure resources.

Example:
- Create AWS VPC
- Create subnets
- Create EC2 instances
- Create load balancers
- Create an EKS cluster

### Ansible — Configuration Management

Ansible configures systems and applications.

Example:
- Install Nginx
- Install required packages
- Configure application settings
- Deploy configuration files
- Start or restart services

Typical workflow:

```text
Terraform
    |
    v
Provision Infrastructure
    |
    v
EC2 / VPC / Load Balancer
    |
    v
Ansible
    |
    v
Configure Servers
    |
    v
Deploy and Run Applications
```

Terraform can provision cloud resources, while Ansible can configure supported systems after they become available.

---

## 5. Containerization vs Container Orchestration

### Docker

Docker helps package and run an application in a container.

Example:

```bash
docker build -t myapp:1.0 .
docker run -d -p 8080:8080 myapp:1.0
```

### Kubernetes

Kubernetes manages containerized workloads across a cluster.

It provides:
- Scheduling
- Desired-state management
- Scaling
- Service discovery
- Rolling updates
- Recovery of failed workloads

Example:

```text
Docker Image
     |
     v
Container Registry
     |
     v
Kubernetes Deployment
     |
     v
Pods
     |
     v
Kubernetes Service
```

Docker is not required as the container runtime in Kubernetes; Kubernetes can use other compatible runtimes, such as containerd or CRI-O.

---

## 6. Prometheus and Grafana

### Prometheus

Prometheus collects metrics from applications, servers, and supported exporters.

Examples of metrics:
- CPU utilization
- Memory usage
- Request rate
- Error rate
- Request latency
- Number of active targets

### Grafana

Grafana queries data sources such as Prometheus and displays dashboards and visualizations.

### Alertmanager

Alertmanager handles Prometheus alerts, including grouping, routing, and notification delivery.

Architecture:

```text
Application / Servers
        |
        v
Metrics Endpoints / Exporters
        |
        v
    Prometheus
        |
        +----------> Alertmanager
        |
        v
      Grafana
        |
        v
Dashboards and Visualization
```

Prometheus collects and stores metrics; Grafana visualizes them; Alertmanager routes alerts.

---

## 7. DevOps Architecture: Putting Everything Together

```text
                 DEVELOPMENT
                      |
                      v
                 Git / GitHub
                      |
                      v
               CI/CD Automation
              Jenkins / GitHub Actions
                      |
             +--------+--------+
             |                 |
             v                 v
        Build & Test      Security Checks
        Maven / Gradle    SonarQube / Scanners
             |
             v
        Container Image
             |
             v
        Container Registry
             |
             v
          Kubernetes
        Helm / Argo CD

INFRASTRUCTURE:
Terraform / OpenTofu
          |
          v
Cloud Resources / Kubernetes Cluster

CONFIGURATION:
Ansible
   |
   v
Server and Application Configuration

OBSERVABILITY:
Prometheus + Grafana + Loki
          |
          v
Metrics / Dashboards / Logs / Alerts
          |
          v
Troubleshooting and Reliability Improvements
```

This is a conceptual architecture. Actual pipelines vary by organization, cloud provider, application, and deployment strategy.

---

## 8. Recommended Learning Order

For someone building an AWS DevOps and SRE foundation, learn the tools in this order:

1. Linux and networking fundamentals
2. Git and GitHub
3. Bash scripting and basic Python
4. Application and build fundamentals
5. Maven or Gradle
6. CI/CD with Jenkins or GitHub Actions
7. Docker and container registries
8. AWS fundamentals and IAM
9. Terraform
10. Ansible
11. Kubernetes fundamentals
12. Helm and Kubernetes deployment workflows
13. Prometheus and Grafana
14. Logging with Loki or an equivalent stack
15. Security, troubleshooting and reliability engineering
16. End-to-end production-style projects

Learn one tool deeply enough to use it in a working project before adding alternatives.

---

## 9. Summary

| Category | Main Tools | Purpose |
|---|---|---|
| Source control | Git, GitHub | Track and collaborate on code |
| Build | Maven, Gradle | Build and package applications |
| Testing | JUnit, pytest, Selenium | Validate application behavior |
| CI/CD | Jenkins, GitHub Actions | Automate delivery |
| Artifacts | Artifactory, Nexus | Store versioned packages |
| Containers | Docker, Podman | Package and run applications |
| Registries | ECR, Docker Hub, GHCR | Store container images |
| Orchestration | Kubernetes | Manage containerized workloads |
| Packaging | Helm | Package Kubernetes applications |
| GitOps | Argo CD | Reconcile deployments from Git |
| IaC | Terraform, OpenTofu | Provision infrastructure |
| Configuration | Ansible | Configure systems and applications |
| Metrics | Prometheus | Collect and query metrics |
| Visualization | Grafana | Build dashboards |
| Alerting | Alertmanager | Route alerts |
| Logging | Loki, ELK | Search and analyze logs |
| Tracing | OpenTelemetry, Jaeger | Investigate distributed requests |

## Final Goal

The goal of DevOps is not to learn every tool available. It is to understand how source code becomes a tested, deployable application, how infrastructure is provisioned, how workloads are operated, and how reliability is measured and improved.
