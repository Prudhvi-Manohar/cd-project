# Flask Docker App with Jenkins CI/CD

This repository contains a simple Flask application that is containerized using Docker and deployed using Jenkins for continuous integration and continuous deployment (CI/CD). The application is built, tested, and deployed automatically whenever changes are pushed to the repository.

## Project Structure

```
flask-app/
├── app/
│   ├── __init__.py
│   ├── routes.py
│   ├── static/
│   │   └── css/
│   │       └── style.css
│   └── templates/
│       └── index.html
├── docker/
│   └── nginx/
│       ├── Dockerfile
│       └── nginx.conf
├── tests/
│   └── test_app.py
├── .dockerignore
├── .gitignore
├── app.py
├── docker-compose.yml
├── Dockerfile
├── Jenkinsfile
├── README.md
└── requirements.txt
```

## How to Run Locally

### Prerequisites

- Docker installed on your machine
- Docker Compose installed (optional, but recommended)

### Steps

1. **Build the Docker image:**

   ```bash
   docker build -t flask-app:latest .
   ```

2. **Run the container:**

   ```bash
   docker run -d -p 5000:5000 --name flask-container flask-app:latest
   ```

   Or using Docker Compose:

   ```bash
   docker-compose up -d
   ```

3. **Access your application:**

   Open your browser and navigate to [http://localhost:5000](http://localhost:5000)

4. **Stop the container:**

   ```bash
   docker stop flask-container
   docker rm flask-container
   ```

   Or with Docker Compose:

   ```bash
   docker-compose down
   ```

## Jenkins Pipeline

### Prerequisites

- Jenkins server installed and running
- Docker plugin installed in Jenkins
- Jenkins user has permissions to run Docker commands

### Configuration

1. **Create a new Jenkins Pipeline job:**

   - Navigate to Jenkins dashboard
   - Click "New Item"
   - Enter a name for your job
   - Select "Pipeline" and click "OK"

2. **Configure Pipeline source:**

   - Under "Pipeline" section, select "Pipeline script from SCM"
   - Select "Git" as SCM
   - Enter your repository URL
   - Specify branch (e.g., "*/main")
   - Script Path: "Jenkinsfile"
   - Click "Save"

3. **Run the pipeline:**

   - Click "Build Now" to manually trigger the pipeline
   - Or set up webhooks in your Git repository to trigger builds automatically

## Deployment Options

### Manual Deployment

After building your Docker image, deploy it to your server:

```bash
# On your server
docker pull your-registry/flask-app:latest
docker stop flask-production || true
docker rm flask-production || true
docker run -d --name flask-production -p 5000:5000 your-registry/flask-app:latest
```

### Automated Deployment via Jenkins

Your Jenkinsfile already contains deployment steps. When Jenkins runs the pipeline, it will:

- Build the Docker image
- Run tests
- Deploy the container, replacing any existing one

## Troubleshooting Common Issues

- **Application not accessible:**

  - Check if the container is running with `docker ps`

- **Port conflicts:**

  - Ensure no other services are using port 5000, or change the port mapping

- **Jenkins permissions:**

  - Make sure Jenkins user has permissions to run Docker commands

- **Pipeline failures:**

  - Check Jenkins console output for error details

This setup provides a complete workflow from development to deployment for your Flask application using Docker and Jenkins.

## License

This project is licensed under the GNU General Public License - see the [LICENSE](LICENSE) file for details.

## Contact

For any questions or suggestions, please contact [2200080089@kluniversity.in](mailto:2200080089@kluniversity.in).

## Acknowledgments

- [Flask](https://flask.palletsprojects.com/)
- [Docker](https://www.docker.com/)
- [Jenkins](https://www.jenkins.io/)
