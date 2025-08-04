# Flask Azure Docker App

This is a simple Flask web application containerized with Docker and designed to be deployed on an Azure Virtual Machine.

## Features

- Runs a minimal Flask app
- Dockerized with a production-ready image
- Listens on port 5000
- Tested on Azure VM (Ubuntu)

## How to Run Locally

```bash
docker build -t flask-azure-app .
docker run -d -p 5000:5000 flask-azure-app
