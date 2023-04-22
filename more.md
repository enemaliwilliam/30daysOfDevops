## 30 Day DevOps Challenge

Day 1

Set up a GitHub account and create a new repository. Push a simple "Hello World" application to the repository.

Solution

`print("Hello, World!")`

Day 2

Install Docker and create a simple Docker container that runs the "Hello World" application from Day 1.

Solution

```
FROM python:3.9-slim-buster
COPY hello_world.py .
CMD ["python", "hello_world.py"]
```

Day 3: Deploy the Docker container from Day 2 to a cloud provider of your choice (e.g., AWS, GCP, Azure).

Solution: Deploy the Docker container from Day 2 to a cloud provider of your choice. Here's an example command for deploying to AWS Elastic Container Service:

``` 
$ aws ecs create-cluster --cluster-name my-ecs-cluster
$ aws ecr create-repository --repository-name my-ecr-repo
$ docker tag my-image:latest my-account-id.dkr.ecr.us-west-2.amazonaws.com/my-ecr-repo:latest
$ aws ecr get-login-password | docker login --username AWS --password-stdin my-account-id.dkr.ecr.us-west-2.amazonaws.com
$ docker push my-account-id.dkr.ecr.us-west-2.amazonaws.com/my-ecr-repo:latest
$ aws ecs create-service --cluster my-ecs-cluster --service-name my-service --task-definition my-task-definition --desired-count 1

```

Day 4: Set up continuous integration (CI) for the "Hello World" application using a tool of your choice (e.g., Jenkins, Travis CI, CircleCI).

Solution: Set up continuous integration (CI) for the "Hello World" application using a tool of your choice. Here's an example Jenkinsfile:

```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t my-image .'
            }
        }
        stage('Test') {
            steps {
                sh 'docker run my-image python -m unittest discover'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker push my-image'
            }
        }
    }
}

```

Day 5: Add automated tests to the "Hello World" application.

Solution: Add automated tests to the "Hello World" application. Here's an example test file:

```
import unittest
import hello_world

class TestHelloWorld(unittest.TestCase):
    def test_hello_world(self):
        self.assertEqual(hello_world.hello_world(), "Hello, World!")

if __name__ == '__main__':
    unittest.main()

```

Day 6: Set up a monitoring solution (e.g., Prometheus, Grafana) for the "Hello World" application.

Solution: Set up a monitoring solution (e.g., Prometheus, Grafana) for the "Hello World" application. Here's an example Prometheus configuration:

```
global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'hello-world'
    static_configs:
      - targets: ['localhost:8000']

```

Day 7: Use Ansible to automate the deployment of the "Hello World" application.

Solution: Use Ansible to automate the deployment of the "Hello World" application. Here's an example Ansible playbook:

```
---
- name: Deploy hello-world
  hosts: servers
  become: true
  vars:
    image_name: my-image
    container_name: hello-world
    port: 8000
  tasks:
    - name: Pull Docker image
      docker_image:
        name: "{{ image_name }}"
        source: pull
    - name: Stop and remove existing container
      docker_container:
        name: "{{ container_name }}"
        state: absent
      ignore_errors: yes
    - name: Start Docker container
      docker_container:
        name: "{{ container_name }}"
        image: "{{ image_name }}"
        ports:
          - "{{ port }}:{{ port }}"
        restart_policy: always

```


Day 8: Set up log aggregation and analysis (e.g., ELK Stack) for the "Hello World" application.

Solution: Set up log aggregation and analysis (e.g., ELK Stack) for the "Hello World" application. Here's an example Logstash configuration:

```
input {
  tcp {
    port => 5000
    codec => "json"
  }
}

filter {
  # Add custom filters here
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
  }
}


```

Day 9: Use Terraform to provision the infrastructure for the "Hello World" application.

Solution: Use Terraform to provision the infrastructure for the "Hello World" application. Here's an example Terraform configuration:


```
provider "aws" {
  region = "us-west-2"
}

resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "my_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.1.0/24"
}

resource "aws_security_group" "my_sg" {
  vpc_id = aws_vpc.my_vpc.id

  ingress {
    from_port   = 0
    to_port     = 65535
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_ecs_cluster" "my_cluster" {
  name = "my-cluster"
}

resource "aws_ecr_repository" "my_repo" {
  name = "my-repo"
}

resource "aws_ecs_task_definition" "my_task" {
  family                   = "my-task"
  container_definitions    = jsonencode([{
    name      = "my-container"
    image     = "${aws_account_id}.dkr.ecr.us-west-2.amazonaws.com/my-repo:latest"
    memory    = 128
    cpu       = 128
    portMappings = [{
      containerPort = 8000
      hostPort      = 8000
    }]
  }])
}

resource "aws_ecs_service" "my_service" {
  name            = "my-service"
  cluster         = aws_ecs_cluster.my_cluster.id
  task_definition = aws_ecs_task_definition.my_task.arn
  desired_count   = 1

  network_configuration {
    subnets        = [aws_subnet.my_subnet.id]
    security_groups = [aws_security_group.my_sg.id]
  }
}


```

Day 10: Set up a load balancer (e.g., AWS ELB, Nginx) for the "Hello World" application.

Solution: Set up a load balancer (e.g., AWS ELB, Nginx) for the "Hello World" application. Here's an example Nginx configuration

```
worker_processes auto;

events {
  worker_connections 1024;
}

http {
  upstream hello_world {
    server 10.0.1.100:8000;
    server 10.0.1.101:8000;
  }

  server {
    listen 80;
    server_name my-app.com;

    location / {
      proxy_pass http://hello_world;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
  }
}
```

Day 11: Implement automated testing for the "Hello World" application.

Solution: Implement automated testing for the "Hello World" application. Here's an example test script:

```
#!/bin/bash

url="http://my-app.com"

# Test HTTP response code
http_code=$(curl -s -o /dev/null -w "%{http_code}" $url)
if [ $http_code -ne 200 ]; then
  echo "HTTP response code is not 200"
  exit 1
fi

# Test response body
response=$(curl -s $url)
if [ "$response" != "Hello, World!" ]; then
  echo "Response body is not 'Hello, World!'"
  exit 1
fi

echo "Tests passed successfully!"

```

Day 12: Implement continuous integration (CI) for the "Hello World" application.

Solution: Implement continuous integration (CI) for the "Hello World" application. Here's an example Jenkinsfile:

```
pipeline {
  agent {
    docker {
      image 'python:3.9'
    }
  }
  stages {
    stage('Build') {
      steps {
        sh 'pip install -r requirements.txt'
      }
    }
    stage('Test') {
      steps {
        sh 'pytest tests.py'
      }
    }
    stage('Push to ECR') {
      steps {
        withAWS(region: 'us-west-2', role: 'arn:aws:iam::123456789012:role/MyRole') {
          sh '$(aws ecr get-login --no-include-email --region us-west-2)'
          sh 'docker build -t my-image .'
          sh 'docker tag my-image:latest 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-image:latest'
          sh 'docker push 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-image:latest'
        }
      }
    }
    stage('Deploy') {
      steps {
        withAWS(region: 'us-west-2', role: 'arn:aws:iam::123456789012:role/MyRole') {
          sh 'ansible-playbook deploy.yml'
        }
      }
    }
  }
}

```


Day 13: Implement continuous deployment (CD) for the "Hello World" application.

Solution: Implement continuous deployment (CD) for the "Hello World" application. Here's an example Jenkinsfile:

```
pipeline {
  agent {
    docker {
      image 'python:3.9'
    }
  }
  stages {
    stage('Build') {
      steps {
        sh 'pip install -r requirements.txt'
      }
    }
    stage('Test') {
      steps {
        sh 'pytest tests.py'
      }
    }
    stage('Push to ECR') {
      steps {
        withAWS(region: 'us-west-2', role: 'arn:aws:iam::123456789012:role/MyRole') {
          sh '$(aws ecr get-login --no-include-email --region us-west-2)'
          sh 'docker build -t my-image .'
      sh 'docker tag my-image:latest 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-image:latest'
      sh 'docker push 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-image:latest'
    }
  }
}
stage('Deploy to dev') {
  when {
    branch 'dev'
  }
  steps {
    withAWS(region: 'us-west-2', role: 'arn:aws:iam::123456789012:role/MyRole') {
      sh 'ansible-playbook deploy-dev.yml'
    }
  }
}
stage('Deploy to prod') {
  when {
    branch 'main'
  }
  steps {
    withAWS(region: 'us-west-2', role: 'arn:aws:iam::123456789012:role/MyRole') {
      sh 'ansible-playbook deploy-prod.yml'
    }
  }
}


```
