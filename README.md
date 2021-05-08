# Jenkins CI/CD 

## Steps involved

1. Create ec2 instance

2. Install openjdk-11

```bash
sudo apt update
sudo apt search openjdk
sudo apt install openjdk-11-jdk
sudo apt install openjdk-11-jdk
```
3. Install and start jenkins 

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl daemon-reload
sudo systemctl start jenkins
sudo systemctl status jenkins
```
4. Install Docker

```bash
sudo apt update
sudo apt install docker.io
```
5. Add jenkins user to docker

```bash
sudo groupadd docker
sudo usermod -aG docker jenkins
```

6. Install pip and ansible

```bash
sudo apt update
sudo apt install python3-pip
sudo pip3 install ansible
```
7. Install and setup nginx reverse proxy

```bash
sudo apt update
sudo apt install nginx

```
/etc/nginx/nginx.conf

```bash

events {
  worker_connections  4096;  ## Default: 1024
}
http {
server {
        listen   80;

        location / {
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $host;
                proxy_set_header X-NginX-Proxy true;
                proxy_pass http://localhost:8080/;
        }
}
}

```

8. Install kubectl and aws cli

```bash

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
9. Spin up kubernetes cluster with kops
create s3 bucket
install kops
```bash

ssh-keygen -f .ssh/id_rsa

```