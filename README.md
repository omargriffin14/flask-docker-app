# Flask Docker App - CI/CD Pipeline with Jenkins

A complete 2-tier web application built with Python Flask (frontend) and MySQL (database), fully containerized with Docker and automated with Jenkins CI/CD pipeline on Microsoft Azure infrastructure.

## Project Overview

This project demonstrates a production-ready CI/CD workflow where a Flask web application is automatically built, containerized, and deployed via Jenkins whenever code is pushed to GitHub. The application allows users to manage tasks in a persistent MySQL database.

**Current Status**: Pipeline building and deploying successfully on Azure VM (20.110.202.160)

## Infrastructure

### Architecture
- **Platform**: Microsoft Azure Virtual Machine (migrated from AWS EC2 due to nested virtualization requirements)
- **VM Type**: Supports Docker Desktop, Jenkins, and full CI/CD pipeline
- **IP Address**: 20.110.202.160

### Key Components Installed
- Docker & Docker Desktop
- Jenkins (CI/CD automation)
- Java 25 (required by Jenkins)
- MySQL 8.0 (database)
- Git for Windows
- Python 3.11

## Tech Stack

- **Frontend**: Python Flask web framework
- **Database**: MySQL 8.0 with persistent volumes
- **Containerization**: Docker & Docker Compose
- **CI/CD**: Jenkins with GitHub integration
- **Version Control**: GitHub (automatic builds on push to main branch)

## Project Structure

```
flask-docker-app/
├── app/
│   ├── app.py                 # Flask application with SQLAlchemy models
│   ├── templates/
│   │   └── index.html         # Frontend HTML with Jinja2 templating
│   ├── Dockerfile             # Flask container build instructions
│   └── requirements.txt        # Python dependencies
├── docker-compose.yml         # Multi-container orchestration
├── Jenkinsfile                # CI/CD pipeline definition
└── README.md
```

## Key Features

- **Task Management**: Create, view, and persist tasks in a MySQL database
- **Automatic Deployment**: Jenkins pipeline triggers on GitHub push
- **Database Persistence**: MySQL data persists across container rebuilds via volumes
- **Health Checks**: MySQL readiness checks prevent Flask connection failures
- **Retry Logic**: Automatic database reconnection attempts with configurable delays
- **Multi-Container**: Flask and MySQL communicate over isolated Docker network

## Setup & Deployment

### Jenkins Pipeline Stages
1. **Checkout**: Clones the latest code from GitHub main branch
2. **Build Docker Image**: Builds Flask container with all dependencies
3. **Deploy Containers**: Stops old containers, brings up fresh Flask + MySQL stack

### Automatic Triggers
- Pipeline runs automatically whenever code is pushed to the `main` branch
- GitHub Personal Access Token (PAT) enables webhook-based triggering

## Application Details

### Flask Application (app.py)
- SQLAlchemy ORM for database interactions
- Automatic table creation on startup
- Database retry logic (5 attempts, 5-second delays)
- Routes: `/` (task list), `/add` (create task)

### Database (MySQL)
- Service: `db` container on internal Docker network
- Database: `flaskdb`
- Authentication: caching_sha2_password (requires cryptography package)
- Healthcheck: Verifies readiness before Flask connects

### Frontend (index.html)
- Jinja2 templating for dynamic task rendering
- Form submission for new task creation
- Clean, simple task list interface

## Dependencies

### Python Packages (requirements.txt)
- `flask` - Web framework
- `flask-sqlalchemy` - ORM layer
- `pymysql` - Pure Python MySQL driver (no C compilation needed)
- `cryptography` - Required for MySQL 8.0 authentication

### System Requirements
- Docker Desktop running
- Jenkins service active
- MySQL accessible via internal Docker network

## Troubleshooting

### Common Issues & Resolutions

**Port 3306 Conflict**
- MySQL ports removed from docker-compose.yml db service since containers communicate internally
- Only external access needs port mapping

**Database Connection Refused**
- Added MySQL healthcheck to docker-compose.yml
- Flask retry logic handles timing gaps during startup
- Ensure MySQL container is healthy before Flask connects

**ModuleNotFoundError: flask_mysqldb**
- Replaced with pymysql + flask-sqlalchemy for clean Docker builds
- flask-mysqldb requires C compilation which fails in slim Python containers

**Cryptography Package Missing**
- MySQL 8.0 uses caching_sha2_password authentication by default
- Added cryptography package to requirements.txt

**Docker Desktop Virtualization Errors**
- Migrated from AWS EC2 (m7i-flex.large doesn't support nested virtualization)
- Azure VM instance supports nested virtualization for Docker Desktop

## Deployment Status

✅ **Operational** (as of March 24, 2026)
- Jenkins pipeline: Building and deploying successfully
- Flask app: Running and healthy in Docker container
- MySQL: Persisting data successfully
- Database operations: Fully functional

⏳ **Pending**
- Public access via Azure NSG (pending network policy updates)
- App is internally reachable; external access requires NSG inbound rule for port 5000

## Next Steps

1. Configure Azure Network Security Group (NSG) inbound rule for port 5000
2. Verify public app access via http://20.110.202.160:5000
3. End-to-end functionality testing from external network

## CI/CD Workflow

```
GitHub Push (main branch)
    ↓
Jenkins Webhook Triggered
    ↓
Clone Repository
    ↓
Build Docker Image (Flask + dependencies)
    ↓
Deploy Containers (docker-compose up -d)
    ↓
Flask App Running on port 5000
↓
MySQL Data Persisted in Volume
```

## References

- [Flask Documentation](https://flask.palletsprojects.com/)
- [SQLAlchemy ORM](https://www.sqlalchemy.org/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Jenkins Documentation](https://www.jenkins.io/doc/)