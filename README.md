This is a Project from DevOps Roadmap......It is project on SSH REMOTE SERVER SETUP to Setup a basic remote linux server and configure it to allow SSH.....


Requirements.


I am required to setup a remote linux server and configure it to allow SSH connections.

    I can register and setup a remote linux server on any provider e.g. a simple droplet on DigitalOcean which gives you $200 in free credits with the link. Alternatively, I can use AWS or any other provider.

    Create two new SSH key pairs and add them to your server.

    I should be able to connect to your server using both SSH keys.

I should should be able to use the following command to connect to your server using both SSH keys.

(ssh -i <path-to-private-key> user@server-ip)

  SOLUTIONS.
---------------------------------------------------

I did not have access to the AWs account and digital ocean......So I make use of Two docker containers using Ubuntu Image:latest to build the two servers....


Steps taken in bulding the two containers
------------------------------------------------

Step 1: I Installed Docker on System using the commands:

I first updated my package list using :::: sudo apt update.....
----------------------------------------------------

I moved ahead ti Install docker using :::::;sudo apt install docker.io -y
----------------------------------------------------------------------

I enabled docker to run on boot using :: sudo systemctl enable docker
------------------------------------------------------------------

I verified that the docker is installed using : docker --version
-----------------------------------------------------------------

Step 2: Create Two Linux Containers (Simulating Servers)


I pulled the Ubuntu  Image : docker pull ubuntu:latest
--------------------------------------------------

I created the two servers using ::::::::

docker run -dit --name server1 ubuntu:latest
docker run -dit --name server2 ubuntu:latest
---------------------------------------------------------

I listed the running containers (Servers) to confirm using::

docker ps
--------------------------------------------------------------

Step 3: Install SSH in Both Containers
I Accessed the first container.......

docker exec -it server1 bash
I Updated packages and install OpenSSH: Inside the first container:

apt update && apt install openssh-server -y
----------------------------------------------
I repeated the same thing for the second process.....

Step 4: Create SSH Keys on Your Local Machine
---------------------------------------
We’ll generate two sets of SSH key pairs on your local machine to connect to the containers.
Create the first key pair (for server1):
ssh-keygen -t rsa -b 4096 -f ~/.ssh/server1_key -C "SSH Key for Server1"
--------------------------------------------------------------------
Create the second key pair (for server2):
ssh-keygen -t rsa -b 4096 -f ~/.ssh/server2_key -C "SSH Key for Server2"
-------------------------------------------------------------------

Step 5: Add Public Keys to the Containers

We need to copy the public keys from your local machine to each container’s authorized_keys file.

docker exec -it server1 bash
------------------------
mkdir -p ~/.ssh
echo "YOUR_FIRST_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
------------------------------

Replace YOUR_FIRST_PUBLIC_KEY_HERE with the output of:
cat ~/.ssh/server1_key.pub
---------------------------------------------
Copy the public key for server2: Exit from server1 and connect to server2:
docker exec -it server2 bash
mkdir -p ~/.ssh
echo "YOUR_SECOND_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
--------------------------------------

Replace YOUR_SECOND_PUBLIC_KEY_HERE with the output of:
cat ~/.ssh/server2_key.pub
---------------------------------

Step 6: Get the IP Address of the Containers
Each Docker container has its own private IP. To connect to the containers over SSH, you’ll need their IP addresses.

docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' server1
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' server2
----------------------------------------------------------------------------------

Take note of the IPs. For example:

    server1: 172.17.0.2
    server2: 172.17.0.3

Step 7: Connect to the Containers via SSH

Now that everything is set up, let’s test the SSH connections.

Connect to server1 using the first key:
ssh -i ~/.ssh/server1_key root@172.17.0.2

Connect to server2 using the second key:
ssh -i ~/.ssh/server2_key root@172.17.0.3
-----------------------------------------------------------------

The connection was successful, and I was able to log  into the containers via SSH

This is the end of the project.
