# jenkins-101

A hands-on learning repository for Jenkins using Docker and Python. This project contains Dockerfile(s) to build a Jenkins environment and Python examples/scripts used for demonstration and CI pipeline testing. The repository is intended for experimenting with Jenkins pipelines, building Docker images, running Python tests, and learning CI/CD basics.

## Table of contents
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Quick start (build & run)](#quick-start-build--run)
- [Accessing Jenkins](#accessing-jenkins)
- [Python examples / tests](#python-examples--tests)
- [Suggested Jenkins pipeline (Jenkinsfile)](#suggested-jenkins-pipeline-jenkinsfile)
- [Project structure (expected)](#project-structure-expected)
- [Best practices](#best-practices)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## Overview
This repository demonstrates a simple Jenkins setup delivered via Docker (custom image or small environment) and includes Python code to exercise CI flows (unit tests, linting, packaging). Use it as a sandbox to:
- Build and run Jenkins locally with Docker
- Create and test Jenkins pipelines that build/test/deploy Python projects or Docker images
- Learn how to persist Jenkins data and configure agents

## Prerequisites
- Docker (Engine) installed and running
- (Optional) Docker Compose if the repo includes a docker-compose.yml
- git
- Python 3.8+ for running example scripts/tests locally
- Basic familiarity with Jenkins concepts (jobs, pipelines, agents)

## Quick start (build & run)
If this repository contains a Dockerfile that creates a Jenkins image, you can build and run it locally:

Build the image:
docker build -t jenkins-101:local .

Run the container (recommended to persist Jenkins data):
docker run -d --name jenkins-101 \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins-101:local

Notes:
- Mounting a named volume (jenkins_home) persists plugin config, jobs, and credentials.
- If a prebuilt image is preferred, you can use the official Jenkins image:
  docker run -d --name jenkins -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts

If the repo includes docker-compose.yml, start it with:
docker-compose up --build

## Accessing Jenkins
- Open http://localhost:8080 in your browser.
- On first startup, Jenkins shows an initialAdminPassword stored at:
  /var/jenkins_home/secrets/initialAdminPassword
  If you are using the container, retrieve it with:
  docker exec jenkins-101 cat /var/jenkins_home/secrets/initialAdminPassword

After unlocking, install suggested plugins and create an admin user or use the built-in setup flow.

## Python examples / tests
If the repository includes Python example code, use a virtual environment and run tests locally:

Create and activate a venv:
python3 -m venv .venv
# macOS / Linux
source .venv/bin/activate
# Windows (PowerShell)
.venv\Scripts\Activate.ps1

Install dependencies (if requirements.txt exists):
pip install -r requirements.txt

Run tests (common examples):
pytest -q

You can use these scripts/tests inside Jenkins pipeline stages to demonstrate running unit tests, linting, and packaging.

Adjust steps for Windows agents if necessary and include credentials in Jenkins for any registry pushes.

## Project structure (expected)
- Dockerfile                 — builds Jenkins or lesson-specific image
- scripts/                   — helper scripts (install, bootstrap)
- examples/ or src/          — Python example code and tests
- requirements.txt           — Python dependencies for examples (may be absent)
- Jenkinsfile                — example pipeline (if present)
- README.md                  — this file

(Actual file names may vary; inspect the repo tree for exact names.)

## Best practices
- Persist Jenkins data using a volume: /var/jenkins_home
- Use credentials store in Jenkins rather than hard-coding tokens
- Keep pipelines declarative and modularize common steps into shared libraries
- Pin Python and dependency versions to reproducible builds (requirements.txt or Poetry)
- Keep Docker images small and secure (use official base images and minimize layers)

## Troubleshooting
- If Jenkins container fails to start, inspect logs:
  docker logs -f jenkins-101
- If initialAdminPassword not found, ensure the Jenkins container had write permission on the mounted volume and check container logs for errors.
- Docker build permissions: run Docker with sufficient permissions or use a user with access to the Docker daemon.

## Contributing
Contributions welcome:
1. Fork the repo and create a feature branch.
2. Add examples, tests, or docs.
3. Run existing examples locally and in a CI job.
4. Open a pull request with clear description and small focused changes.

If you add long-running training or large artifacts, store them elsewhere (releases, S3, etc.) and include small sample data for CI.

## License
If the repo does not include a LICENSE file, add one (MIT, Apache-2.0, etc.) to clarify usage and contributions.

## Contact
Open an issue in this repository for questions or suggestions, or contact the repository owner.

