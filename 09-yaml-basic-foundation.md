# YAML Fundamentals for DevOps Engineers

## 1. What Is YAML?

YAML stands for **YAML Ain't Markup Language**. It is a human-readable data serialization language used to store configuration data.

YAML is commonly used to define application configurations, infrastructure settings, automation tasks, and CI/CD pipelines.

### Where Is YAML Used in DevOps?

| Technology | YAML Usage |
|---|---|
| Docker Compose | Define services, networks, and volumes |
| Kubernetes | Define Pods, Deployments, Services, and ConfigMaps |
| Ansible | Define playbooks, tasks, and variables |
| GitHub Actions | Define CI/CD workflows |
| GitLab CI/CD | Define pipeline jobs and stages |
| AWS CloudFormation | Supports YAML templates |
| Helm | Configure Kubernetes applications using values files |

## 2. YAML File Extensions

Common YAML file extensions:

- `.yaml`
- `.yml`

Both extensions are valid.

Examples:

```text
docker-compose.yaml
deployment.yaml
playbook.yml
values.yaml
```

## 3. YAML Basic Syntax

YAML uses indentation to represent relationships between data.

Important rules:

1. Use spaces for indentation, not tabs.
2. Use consistent indentation, commonly two spaces.
3. Use a colon (`:`) to separate a key and its value.
4. Use a hyphen (`-`) to represent an item in a list.
5. Indentation determines nesting.
6. YAML is case-sensitive.
7. Comments begin with `#`.

Example:

```yaml
name: devops-engineer
age: 32
role: cloud-engineer
```

Here, `name`, `age`, and `role` are keys. Their corresponding values are `devops-engineer`, `32`, and `cloud-engineer`.

## 4. Key-Value Pairs

A key-value pair stores data under a named key.

```yaml
name: nginx
image: nginx:stable
port: 80
enabled: true
```

Explanation:

- `name` identifies the service.
- `image` specifies the container image.
- `port` defines a port value.
- `enabled` is a Boolean value.

## 5. YAML Data Types

### String

Textual data.

```yaml
name: nginx
environment: production
```

Strings can be quoted when needed:

```yaml
message: "Hello DevOps"
version: '1.0'
```

### Integer

Whole numbers.

```yaml
port: 8080
replicas: 3
```

### Float

Numbers with decimal points.

```yaml
cpu_limit: 1.5
memory_gb: 2.5
```

### Boolean

True or false values.

```yaml
enabled: true
debug: false
```

Prefer `true` and `false` for clear, portable YAML.

### Null

Represents an empty or absent value.

```yaml
database_password: null
```

### Important: Quoted vs Unquoted Values

```yaml
port: 8080
version: "1.0"
employee_id: "00123"
```

Quoted values are strings. Quoting identifiers such as `"00123"` helps preserve leading zeros.

## 6. Lists (Sequences)

A list contains multiple values. Each item begins with a hyphen followed by a space.

```yaml
tools:
  - Git
  - Docker
  - Kubernetes
  - Terraform
```

Another valid representation is:

```yaml
tools: [Git, Docker, Kubernetes, Terraform]
```

The first format is generally easier to read for longer lists.

### DevOps Example: Multiple Ports

```yaml
ports:
  - 80
  - 443
  - 8080
```

## 7. Dictionaries (Mappings)

A dictionary stores related key-value pairs.

```yaml
developer:
  name: Arun
  role: DevOps Engineer
  experience: 1
```

Here, `developer` contains three nested keys.

### Nested Data

```yaml
application:
  name: payment-service
  environment: production
  database:
    host: db.example.internal
    port: 5432
```

The `database` mapping is nested inside `application`.

## 8. Lists of Dictionaries

This structure is very common in Kubernetes, Docker Compose, and Ansible.

```yaml
employees:
  - name: Arun
    role: DevOps Engineer
  - name: Priya
    role: Cloud Engineer
```

Each list item is a mapping with its own keys.

### Example: Application Containers

```yaml
services:
  - name: frontend
    image: nginx:stable
    port: 80
  - name: backend
    image: python:3.12-slim
    port: 8000
```

## 9. Comments in YAML

Comments begin with `#`.

```yaml
# Application configuration
name: payment-service

# Number of replicas
replicas: 3
```

Comments explain the configuration and are ignored as data by YAML parsers.

## 10. Multiline Strings

YAML supports multiline text.

### Literal Block: `|`

Preserves line breaks.

```yaml
script: |
  echo "Starting application"
  echo "Checking service"
  echo "Deployment completed"
```

### Folded Block: `>`

Usually folds line breaks into spaces.

```yaml
description: >
  This application handles
  customer payments and
  order processing.
```

Use `|` for multiline shell scripts and `>` for readable paragraphs. Exact folding behavior depends on indentation and blank lines.

## 11. Environment Variables in YAML

Example configuration:

```yaml
environment:
  APP_ENV: production
  APP_PORT: "8080"
  LOG_LEVEL: info
```

In Docker Compose, this defines environment variables for a service.

**Security rule:** Do not commit real passwords, API keys, or access tokens to GitHub. Use a secrets manager or the secret-handling mechanism supported by your platform.

## 12. YAML Anchors and Aliases — Basic Awareness

Anchors and aliases let you reuse a YAML node.

```yaml
defaults: &app_defaults
  restart: unless-stopped
  environment:
    APP_ENV: production

service_a:
  <<: *app_defaults
  image: nginx:stable
```

- `&app_defaults` defines an anchor.
- `*app_defaults` references it.
- `<<` is the merge key supported by many YAML parsers.

Support and merge behavior can vary by parser and YAML version, so check the target tool's documentation.

## 13. YAML in Docker Compose

Example `compose.yaml`:

```yaml
services:
  web:
    image: nginx:stable
    ports:
      - "8080:80"
    restart: unless-stopped
```

Explanation:

- `services` defines the containers managed by Compose.
- `web` is the service name.
- `image` identifies the container image.
- `ports` maps host port `8080` to container port `80`.
- `restart` defines the restart policy.

Commands:

```bash
docker compose config
docker compose up -d
docker compose ps
docker compose logs web
docker compose down
```

`docker compose config` renders and validates the Compose configuration. It may also resolve variables and display sensitive values, so review its output carefully.

## 14. YAML in Kubernetes

Example `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 2

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx:stable
          ports:
            - containerPort: 80
```

Explanation:

- `apiVersion` identifies the Kubernetes API version.
- `kind` specifies the resource type.
- `metadata` contains resource identity information.
- `spec` describes the desired state.
- `replicas` specifies the desired number of Pods.
- `selector` identifies the Pods managed by the Deployment.
- `template` defines the Pod template.
- `containers` specifies the container configuration.

Commands:

```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods
kubectl describe deployment nginx-deployment
kubectl delete -f deployment.yaml
```

## 15. YAML in Ansible

Example `playbook.yml`:

```yaml
---
- name: Install and start Nginx
  hosts: webservers
  become: true

  tasks:
    - name: Install Nginx
      ansible.builtin.package:
        name: nginx
        state: present

    - name: Start Nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

Explanation:

- `---` marks the beginning of a YAML document.
- `name` describes the play or task.
- `hosts` specifies the target inventory group.
- `become` requests privilege escalation.
- `tasks` contains the operations to execute.
- `ansible.builtin.package` installs the package.
- `ansible.builtin.service` manages the service.

Validate the playbook syntax:

```bash
ansible-playbook --syntax-check playbook.yml
```

## 16. YAML in GitHub Actions

Example `.github/workflows/ci.yml`:

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Run a command
        run: echo "CI pipeline started"
```

Explanation:

- `name` identifies the workflow.
- `on` specifies the events that trigger it.
- `push` triggers the workflow when changes are pushed.
- `jobs` defines jobs in the workflow.
- `runs-on` selects the runner.
- `steps` lists the actions and commands to execute.

Note: YAML parsing and GitHub Actions workflow validation are different checks. A file can be valid YAML but still contain invalid workflow configuration.

## 17. Common YAML Errors

### Error 1: Incorrect Indentation

Incorrect:

```yaml
services:
- name: web
  image: nginx
    port: 80
```

Correct:

```yaml
services:
  - name: web
    image: nginx
    port: 80
```

### Error 2: Using Tabs

Incorrect indentation with tabs may cause parsing errors.

Use spaces consistently.

### Error 3: Missing Space After Colon

Incorrect:

```yaml
name:nginx
```

Correct:

```yaml
name: nginx
```

### Error 4: Inconsistent List Structure

Incorrect:

```yaml
tools:
  - Git
  Docker
```

Correct:

```yaml
tools:
  - Git
  - Docker
```

### Error 5: Incorrect Boolean or String Assumptions

```yaml
enabled: true
employee_id: "00123"
```

Quote values when you need them to remain strings, especially identifiers and values with leading zeros.

### Error 6: Valid YAML, Invalid Application Configuration

A YAML parser can accept a file even when a tool rejects a field name, API version, indentation-sensitive structure, or unsupported option.

Always validate with the target tool.

## 18. How to Validate YAML

Choose the validator appropriate for the tool.

### Docker Compose

```bash
docker compose config
```

### Ansible

```bash
ansible-playbook --syntax-check playbook.yml
```

### Kubernetes

```bash
kubectl apply --dry-run=client -f deployment.yaml
```

For stronger Kubernetes checks, validate against the API server or use schema-aware tooling.

### Generic YAML Parsing with Python

Install PyYAML in a suitable virtual environment:

```bash
python -m pip install PyYAML
```

Create `validate_yaml.py`:

```python
import sys
import yaml

with open(sys.argv[1], encoding="utf-8") as file:
    yaml.safe_load(file)

print("YAML syntax is valid")
```

Run:

```bash
python validate_yaml.py compose.yaml
```

This checks parsing, not whether Docker Compose, Kubernetes, or another application accepts the configuration.

## 19. YAML vs JSON

| Feature | YAML | JSON |
|---|---|---|
| Readability | Designed to be human-friendly | Structured, with more punctuation |
| Indentation | Defines nesting | Does not define nesting |
| Comments | Supported | Not supported by standard JSON |
| Lists and objects | Supported | Supported |
| Common DevOps use | Configuration and automation | APIs and data exchange |

Both formats represent structured data. YAML is especially common in DevOps configuration files.

## 20. YAML Best Practices

1. Use consistent two-space indentation unless the tool specifies otherwise.
2. Never use tabs for indentation.
3. Quote ambiguous strings, identifiers with leading zeros, and values whose exact string representation matters.
4. Keep YAML files readable and logically structured.
5. Avoid storing secrets directly in repository files.
6. Validate configuration before deployment.
7. Use version control to track configuration changes.
8. Review indentation carefully during code reviews.
9. Follow the schema and syntax of the target tool.
10. Pin action versions and container image versions appropriately for reproducible deployments.

## 21. Practice Exercises

### Exercise 1: Basic Data

Create `developer.yaml` containing:

- Name
- Role
- Experience
- A list of five DevOps tools
- A Boolean field named `learning`

### Exercise 2: Nested Configuration

Create `application.yaml` with:

- Application name
- Environment
- Database host and port
- Two application instances, each with a name and port

### Exercise 3: Docker Compose

Create a Compose file that runs Nginx and maps host port `8080` to container port `80`.

Validate it with:

```bash
docker compose config
```

### Exercise 4: Kubernetes

Create a Deployment manifest for Nginx with two replicas. Apply it to a test Kubernetes cluster and inspect the Pods.

### Exercise 5: Ansible

Write a playbook that installs Nginx and ensures its service is running.

Validate the syntax before executing it.

## 22. Interview Questions

1. What is YAML, and why is it used in DevOps?
2. What is the difference between a YAML list and a mapping?
3. Why is indentation important in YAML?
4. Can tabs be used for indentation?
5. What is the difference between `|` and `>`?
6. What are YAML anchors and aliases?
7. How do you validate a Docker Compose YAML file?
8. How is YAML used in Kubernetes?
9. What is the difference between valid YAML and a valid Kubernetes manifest?
10. How should secrets be managed in YAML-based pipelines?

## 23. Learning Summary

YAML is a foundational configuration format for DevOps engineering. You should be comfortable reading, writing, editing, and validating YAML before moving deeper into Kubernetes, Ansible, Docker Compose, and CI/CD.

**Recommended next step:** Practise basic YAML syntax, then write one Docker Compose file, one Ansible playbook, and one Kubernetes manifest.
