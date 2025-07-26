Here's the markdown format for your project information:

```markdown
# 🚀 Dockerized Flask WebApp Deployment using Ansible 

This project demonstrates how to automate the deployment of a Dockerized Flask web application using Ansible on an AWS EC2 instance. It also includes a multi-container deployment setup using Ansible.


---

## 📌 **Project Overview**

| Feature | Status | Assignee | Scheduled |
|--------|--------|----------|-----------|
| ✅ Deploy a Single Containerized Flask App Using Ansible | 🔴 Not complete | Piyush Bankhede | – |
| ✅ Push Docker Image to Docker Hub Automatically via Ansible | 🔴 Not complete | Piyush Bankhede | – |
| ✅ Control Container Lifecycle from Ansible | 🔴 Not complete | Piyush Bankhede | – |
| ✅ Multi-container Support with an Alternate Playbook | 🔴 Not complete | Piyush Bankhede | – |
| ✅ Manual EC2 Setup, No Orchestration Frameworks Used | 🔴 Not complete | Piyush Bankhede | – |

---

## 🛠️ **Prerequisites**

- **EC2 Instance:** `t3.medium`, Ubuntu 22.04
- **Docker:** Installed manually
- **Ansible:** Installed manually
- **Docker Hub Account:** `piyushbankede`



---

## 📁 **Project Structure**


```
ansible-flask-deploy/
├── webapp/
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
└── playbook/
    ├── inventory.ini
    ├── deploy.yaml
    └── deploy-multi-container.yaml
```

## 🧠 Step-by-Step Instructions

1. **Create EC2 Instance**
    - **Instance Type:** t3.medium
    - **OS:** Ubuntu 22.04 LTS
    - **Inbound Security Group:** All traffic allowed (testing purpose only)

2. **Install Docker & Ansible**
    - SSH into the EC2 instance and run:
      ```shell
      # Install Docker
      sudo apt update && sudo apt install -y docker.io
      sudo systemctl enable docker
      sudo usermod -aG docker $USER

      # Install Ansible
      sudo apt install -y ansible
      ```

3. **Add Project Files**
    - **webapp/app.py**
      ```python
      from flask import Flask
      app = Flask(__name__)

      @app.route('/')
      def home():
          return "🚀 Flask app deployed using Ansible and Docker!"

      if __name__ == "__main__":
          app.run(host="0.0.0.0", port=5000)
      ```
    - **webapp/requirements.txt**
      ```
      flask
      ```
    - **webapp/Dockerfile**
      ```dockerfile
      FROM python:3.9-slim
      WORKDIR /app
      COPY . .
      RUN pip install -r requirements.txt
      EXPOSE 5000
      CMD ["python", "app.py"]
      ```

4. **Ansible Inventory**
    - **playbook/inventory.ini**
      ```
      [web]
      localhost ansible_connection=local
      ```

5. **Single Container Playbook**
    - **playbook/deploy.yaml**
      ```yaml
      - name: Deploy Flask App in Docker using Ansible
        hosts: web
        tasks:
          - name: Copy app files
            copy:
              src: ../webapp/
              dest: /home/ubuntu/webapp/
              mode: 0755

          - name: Build Docker Image
            shell: docker build -t piyushbankhede/ansible-dockerized-app:latest /home/ubuntu/webapp

          - name: Login to DockerHub
            shell: echo "{{ dockerhub_password }}" | docker login -u {{ dockerhub_user }} --password-stdin
            vars:
              dockerhub_user: piyushbankhede
              dockerhub_password: "{{ lookup('env','DOCKER_PASSWORD') }}"

          - name: Push Image to DockerHub
            shell: docker push piyushbankhede/ansible-dockerized-app:latest

          - name: Run Container
            shell: docker run -d -p 5000:5000 --name flask-app piyushbankhede/ansible-dockerized-app:latest
      ```

6. **Multi-Container Playbook**
    - **playbook/deploy-multi-container.yaml**
      ```yaml
      - name: Deploy Multiple Flask Containers using Ansible
        hosts: web
        tasks:
          - name: Deploy 3 Flask containers
            shell: |
              for i in 1 2 3; do
                docker run -d -p 500$i:5000 --name flask-app-$i piyushbankhede/ansible-dockerized-app:latest
              done
      ```

## 🚀 Deployment Commands

### ✅ Deploy Single Container
```bash
ansible-playbook -i playbook/inventory.ini playbook/deploy.yaml
# Access app on: http://<your-ec2-ip>:5000
```

### ✅ Deploy Multiple Containers
```bash
ansible-playbook -i playbook/inventory.ini playbook/deploy-multi-container.yaml
# Access on:
# http://<your-ec2-ip>:5001
# http://<your-ec2-ip>:5002
# http://<your-ec2-ip>:5003
```

## ✅ Verification Commands

**Check Ansible is Working**
```bash
ansible all -i playbook/inventory.ini -a "hostname"
# Should return the EC2 hostname.
```

**Check Hostnames of All Running Containers**
```bash
ansible all -i playbook/inventory.ini -m shell -a \
"docker ps -q | xargs -n1 docker inspect --format='{{ '{{' }}.Name{{ '}}' }} => {{ '{{' }}.Config.Hostname{{ '}}' }}'"
```

## 📦 Docker Image Used

**Image:** piyushbankhede/ansible-dockerized-app


- Automated containerized deployment with Ansible ✔️
- DockerHub push via playbook ✔️
- EC2-based manual infra, ready for Kubernetes extension ✔️

## 🧠 Next Steps

- Integrate with Kubernetes via Ansible
- CI/CD pipeline with GitHub Actions
- Use Ansible Vault for secure credentials

## 👨‍💻 Author

** Piyush Bankhede**
- DevOps | Cloud | Automation
```

Feel free to copy and use this markdown text in your project documentation.
