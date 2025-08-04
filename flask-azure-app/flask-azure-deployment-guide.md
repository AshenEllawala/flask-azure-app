
# Flask Azure App - Real World Deployment Guide

## Project Overview
This is a simple Flask application containerized using Docker and deployed on an Azure Virtual Machine (VM). It includes routing through NGINX and deployment best practices.

---

## File Structure

```
flask-azure-app/
│
├── app.py                 # Flask application code
├── Dockerfile             # Dockerfile to build the Flask app image
├── requirements.txt       # Python dependencies
├── README.md              # Project overview and setup steps
```

---

## Step-by-Step Deployment

### ✅ Step 1: Create Azure VM

- Create a Linux (Ubuntu) VM in Azure.
- Open the following ports in Network Security Group (NSG):
  - 22 (SSH)
  - 5000 (Flask)
  - 80 (HTTP for NGINX)

### ✅ Step 2: SSH into VM

```bash
ssh -i flask-key.pem localadmin@<Your-VM-IP>
```

### ✅ Step 3: Install Docker

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

Logout and SSH back in to enable Docker group.

### ✅ Step 4: Clone/Create Flask App

```bash
mkdir flask-app && cd flask-app
nano app.py
```

Paste this content:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello from your Flask app running in Docker on Azure VM!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### ✅ Step 5: Add `requirements.txt`

```txt
flask
```

### ✅ Step 6: Add Dockerfile

```dockerfile
FROM python:3.8-slim
WORKDIR /app
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

### ✅ Step 7: Build & Run Docker

```bash
docker build -t flask-azure-app .
docker run -d -p 5000:5000 flask-azure-app
```

### ✅ Step 8: Install and Configure NGINX

```bash
sudo apt install nginx -y
sudo nano /etc/nginx/sites-available/default
```

Replace the `location /` block with:

```nginx
location / {
    proxy_pass http://127.0.0.1:5000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

Then restart NGINX:

```bash
sudo systemctl restart nginx
```

### ✅ Step 9: Visit the App

Go to `http://<Your-VM-IP>` in your browser. You should see the Flask app's output.

---

## Optional: Push Docker Image to Docker Hub

```bash
docker tag flask-azure-app <your-dockerhub-username>/flask-azure-app
docker login
docker push <your-dockerhub-username>/flask-azure-app
```

---

## Optional: GitHub Upload

If GitHub push fails via VM, upload the files from your local machine instead.

---

## Summary

✅ Azure VM deployed  
✅ Flask app containerized  
✅ Docker container running  
✅ Reverse proxy with NGINX  
✅ Production-style deployment tested  

