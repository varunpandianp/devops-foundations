# DevOps Foundations

The Foundations phase builds the core knowledge required to work effectively as a DevOps, Cloud, or SRE engineer.

It focuses on operating systems, networking, applications, source control, web servers, databases, security, configuration formats, and hands-on lab environments.

---

## 1. Introduction

Understand what DevOps is, why it is important, and how different technologies fit together.

### Topics

- Introduction to DevOps
- DevOps culture and practices
- DevOps lifecycle
- DevOps tools and their purpose
- Development → Build → Test → Release → Deploy → Operate → Monitor
- Role of a DevOps Engineer

### Goal

Understand the overall DevOps ecosystem before learning individual tools.

---

## 2. Linux Basics

Linux is one of the most important foundations for DevOps because most servers, containers, and cloud workloads run on Linux.

### Topics

- Linux architecture
- Linux file system hierarchy
- Basic Linux commands
- Files and directories
- Users and groups
- File ownership
- File permissions
- `chmod`
- `chown`
- Process management
- `ps`
- `top`
- `kill`
- Package management
- `apt`
- `yum`
- `dnf`
- Services and `systemctl`
- Environment variables
- SSH
- Basic Linux troubleshooting

### Goal

Become comfortable administering and troubleshooting Linux servers from the command line.

---

## 3. Networking Basics

Understand how servers communicate with each other and how applications become accessible over a network.

### Topics

- IP addresses
- Private IP vs Public IP
- Subnets
- CIDR
- Network interfaces
- Ports
- TCP/IP
- DNS
- Hostnames
- HTTP
- HTTPS
- Client-server communication
- Listening and binding addresses
- `127.0.0.1`
- `0.0.0.0`
- Basic network troubleshooting

### Example

    host01
       |
       | HTTP :5000
       ↓
    app01
       |
       ↓
    Flask Application

### Goal

Understand how one server communicates with another server and how network requests reach applications.

---

## 4. Applications Basics

Understand how applications are built, started, configured, and accessed.

### Topics

- Application architecture
- Application processes
- Application ports
- Application configuration
- Environment variables
- Application dependencies
- Python applications
- Java applications
- Application logs
- Foreground vs Background processes
- `nohup`
- Process management
- Application troubleshooting
- Client-server applications

### Example

    nohup python3 app.py &

### Goal

Understand what actually happens when an application is deployed and run on a server.

---

## 5. Source Control Management

Learn how developers and DevOps engineers manage application source code.

### Topics

- Git fundamentals
- Git repositories
- `git clone`
- `git add`
- `git commit`
- `git push`
- `git pull`
- Branches
- Merge
- Remote repositories
- GitHub
- Version control workflows
- Git troubleshooting

### Example

    git clone <repository>
    git add .
    git commit -m "Initial commit"
    git push

### Goal

Become comfortable managing source code using Git and GitHub.

---

## 6. Web Server

Understand how web servers receive client requests and serve applications.

### Topics

- Web server fundamentals
- HTTP and HTTPS
- Nginx
- Apache
- Apache Tomcat
- Ports
- Virtual hosts
- Reverse proxy
- Application servers
- Static content
- Dynamic content
- Web server logs
- Web server troubleshooting

### Typical Architecture

    Client
       ↓
    Nginx / Apache
       ↓
    Application
       ↓
    Database

### Goal

Understand how web traffic reaches an application in a real environment.

---

## 7. Database Basics

Learn the fundamentals of databases and how applications interact with them.

### Topics

- What is a database?
- SQL vs NoSQL
- Relational databases
- Tables
- Rows
- Columns
- MongoDB
- Documents
- Collections
- Database connectivity
- Database ports
- Application → Database communication

### Example

    Application
         |
         | :27017
         ↓
      MongoDB

### Goal

Understand the role of databases in application architecture and how applications communicate with databases.

---

## 8. Security

Understand the basic security concepts required when managing servers and applications.

### Topics

- Authentication
- Authorization
- SSH security
- SSH keys
- Passwordless SSH
- File permissions
- TLS
- SSL
- HTTPS
- TLS certificates
- Basic network security

### Example

    Client
      |
      | HTTPS
      ↓
    TLS Certificate
      |
      ↓
    Web Server

### Goal

Understand the basic security mechanisms used to protect infrastructure and applications.

---

## 9. General Prerequisites

Learn additional fundamental concepts required before moving into advanced DevOps technologies.

### Topics

- YAML
- JSON
- JSONPath
- Configuration formats
- Basic scripting concepts
- Data structures
- Dictionaries
- Lists
- List of dictionaries
- Command-line concepts
- APIs
- Structured data

### YAML Example

    server:
      name: app01
      port: 5000

### JSON Example

    {
      "server": {
        "name": "app01",
        "port": 5000
      }
    }

### JSONPath Example

    $.server.name

### Goal

Become comfortable reading, understanding, and manipulating configuration and structured data used by DevOps tools.

---

## 10. 2-Tier Applications

Understand how a typical application communicates with another service, especially a database.

### Architecture

    Network
       |
       ↓
    +----------------+
    |  Application   |
    |     Server     |
    +-------+--------+
            |
            | Database connection
            ↓
    +----------------+
    |    Database    |
    +----------------+

### Topics

- Client-server architecture
- Application tier
- Database tier
- Network communication
- Ports
- Hostnames
- Application configuration
- Database connectivity
- Troubleshooting connectivity

### Goal

Understand the architecture and communication flow of a simple production-style application.

---

## 11. Lab Setup

Learn how to work with a multi-server lab environment and troubleshoot real infrastructure problems.

### Topics

- Server access
- SSH
- Multiple servers
- Hostnames
- IP addresses
- Running applications
- Checking ports
- `curl`
- Process troubleshooting
- Service troubleshooting
- Connectivity testing

### Example

Login to another server:

    ssh app01

Test an application locally:

    curl localhost:5000

Test the application from another server:

    curl app01:5000

Test using the IP address:

    curl 172.16.239.30:5000

### Goal

Develop practical troubleshooting skills instead of learning only theory.

---

## 12. Conclusion

Review the foundation concepts and understand how they connect together before moving into advanced DevOps technologies.

### Overall DevOps Foundation

    Linux
      ↓
    Networking
      ↓
    Applications
      ↓
    Git / GitHub
      ↓
    Web Servers
      ↓
    Databases
      ↓
    Security
      ↓
    YAML / JSON / JSONPath
      ↓
    2-Tier Applications
      ↓
    Hands-on Labs
      ↓
    Advanced DevOps

---

# What I Should Be Able to Do After Foundations

After completing this foundation, I should be comfortable answering and performing tasks such as:

- What happens when I run `ssh app01`?
- What is the difference between `127.0.0.1` and `0.0.0.0`?
- How does `host01` communicate with `app01`?
- What is an IP address?
- What is a port?
- How does DNS resolve a hostname?
- How do I check whether port `5000` is listening?
- How do I troubleshoot `Connection refused`?
- What does `nohup python3 app.py &` do?
- How does Nginx communicate with an application?
- How does an application communicate with MongoDB?
- What is Git and why do we use it?
- What are YAML and JSON?
- What is JSONPath?
- What is TLS and why is HTTPS required?
- How does a 2-tier application work?
- How can I troubleshoot connectivity between two servers?

---

# Next DevOps Learning Path

After completing the foundations, move into the core DevOps technologies:

    DevOps Foundations
            ↓
    Linux + Networking
            ↓
    AWS Cloud
            ↓
    Git + GitHub
            ↓
    Shell / Python
            ↓
    Maven / Build Tools
            ↓
    Docker
            ↓
    CI/CD
            ↓
    Terraform
            ↓
    Ansible
            ↓
    Kubernetes
            ↓
    Helm
            ↓
    Monitoring & Logging
            ↓
    Prometheus + Grafana
            ↓
    AWS DevOps Projects
            ↓
    SRE / Production Engineering

---

## Final Goal

Build strong fundamentals first, then learn DevOps tools on top of those fundamentals.

The objective is not just to memorize commands, but to understand how:

- Applications work
- Servers work
- Networks work
- Databases work
- Source control works
- Security works
- Infrastructure communicates
- Automation works

These foundations will make it much easier to understand AWS, Docker, Kubernetes, Terraform, CI/CD, monitoring, and SRE concepts.