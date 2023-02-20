# redis-setup-aws-ec2

## Overview

### This is a guide to setup redis development stack on an AWS EC2 instance

## Setup

### Step 1:

- Create an AWS EC2 instance as follows

  - **Name : redis-dev**
  - **AWS Machine Image : Amazon Linux 2 Kernel 5.10 / 64 bit (x86)**
  - **Instance type : t2.medium 4g Memory**
  - **Key pair (login) : Create a key pair to SSH login from your host**
  - **Network settings : Create security group & ALLOW SSH traffic from your host ip address**
  - **Configure storage : 15 GB gp2**
  - **Edit Network settings : Disable Auto-assign public IP**

### Step 2:

- Edit security group inbound rules and expose the following ports

  - 6379 → Redis port
  - 8001 → RedisInsight port to visualize your data

### Step 3:

- Follow this guide to create an elastic static public IPv4 address -- > [Create Elastic IPv4 Address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-eips-allocating)

- Then follow this guide to associate the elastic IP address with your redis instance -- > [Associate Elastic IPv4 Address with Redis](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-eips-associating)

### Step 4:

- SSH Log in to your AWS instance & Run the following commands to install docker

  ```bash
  sudo yum -y update && \
  sudo yum install -y git && \
  sudo yum install -y docker && \
  sudo usermod -a -G docker $USER && \
  newgrp docker
  ```

- Start docker systemctl service

  ```bash
  sudo systemctl enable docker.service && \
  sudo systemctl start docker.service
  ```

### Step 5:

- Create Dockerfile

  ```bash
  echo "FROM redis/redis-stack:latest" > Dockerfile
  ```

- Build image

  ```bash
  docker build -f Dockerfile -t redis-stack .
  ```

- Spin up redis container with data persistence

  ```bash
  docker run --name redis-stack -d -p 6379:6379 -p 8001:8001 -v /local-data/:/data redis-stack

  ```

  ### Now your Redis development stack is ready!

### Step 6:

- Connect with redis-cli for testing

  ```bash
    docker exec -it redis-stack redis-cli
  ```

- Run PING and you will get PONG back

  ```bash
  PING
  ```

- You can access RedisInsight UI to visualize your data at -- > [http://localhost:8001](http://localhost:8001)

---

## Resources

Feel free to check these resources for documentaion

https://redis.io/docs/stack/get-started/install/docker/

https://hub.docker.com/r/redis/redis-stack

https://aws.amazon.com/premiumsupport/knowledge-center/ec2-associate-static-public-ip/

https://docs.redis.com/latest/rs/installing-upgrading/hardware-requirements/
