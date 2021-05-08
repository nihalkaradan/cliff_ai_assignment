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
##### create s3 bucket to store state
```bash
s3://kops-state-13
```
Install kops

```bash
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

```
Spin up cluster

```bash
ssh-keygen -f .ssh/id_rsa
export KOPS_STATE_STORE=s3://kops-state-13
kops create cluster mycluster.k8s.local --node-count=1 --node-size=t3.small --master-size=t3.small --zones ap-south-1a --yes
```

10. Create Jenkins pipeline

Fork and create github webhook
```bash

git fork https://github.com/postmanlabs/httpbin

#create webhook in github dashboard to trigger jenkins

```
Create job 

```bash

## Pipeline script
#create dockerhub credential
node {
    
    
    def registry = "nihalkaradan/test-repo"
    def registryCredential = 'dockerhub_id'
  
    stage('Preparation') { // for display purposes
        // Get some code from a GitHub repository
        git 'https://github.com/nihalkaradan/httpbin'
        // Get the Maven tool.
        // ** NOTE: This 'M3' Maven tool must be configured
        // **       in the global configuration.
        
    }
    stage('Build and Publish') {
        // Run the maven build
        
       sh "docker build . -t nihalkaradan/test:3.0"
       withDockerRegistry([ credentialsId: "${registryCredential}", url: "" ]) {
          sh "docker build . -t nihalkaradan/test:3.0"
          sh 'docker push nihalkaradan/test:3.0'
        }
    }
    stage('Deploy on K8s'){
        sh "ansible-playbook playbook.yml  --user=jenkins "
    }
    stage('Results') {
        sh "touch test3"
    }
}


```



