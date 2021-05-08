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
3. Install jenkins

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```
4. Start jenkins

```bash
sudo systemctl daemon-reload
sudo systemctl start jenkins
sudo systemctl status jenkins
```
5. Install Docker

```bash
sudo apt update
sudo apt install docker.io
```
6. Add jenkins user to docker

```bash
sudo groupadd docker
sudo usermod -aG docker jenkins
```

7. Install pip and ansible

```bash
sudo apt update
sudo apt install python3-pip
sudo pip3 install ansible
```
8. Install and setup nginx as reverse proxy

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