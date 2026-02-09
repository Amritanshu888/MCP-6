## Creating EC2 instance
- Search for EC2 instance(They are like virtual servers in the cloud that means here u will be getting virtual computers).
- Once u open it on the next page click on "Launch Instance", give the name of the instance under "Name and Tags" give the name: mcp-server, then under quickstart select ubuntu instance, then under instance type select t2.medium(per hour charge will be given there).
- Next we will have to create a key-value pair click on "Create new key pair" then enter the name mcp, the Key pair type should be "RSA", format should be .pem and then click on "Create Key pair", the .pem file will be automatically downloaded
- Then under network settings allow all the three options: Allow SSH Traffic from(Helps u connect to your instance), Allow HTTPS traffic from the internet(To set up an endpoint for example when creating a web server), Allow HTTP traffic from the internet(To set up an endpoint for example when creating a web server)
- Configure Storage: 8 GB
- Then at last click on "Launch Instance"
- Then click on "View All Instances" ---> Ur instance will appear there
- Hence we have successfully created our EC2 Instance.

- Next click on the instance_id of the created instance, here on the top right we have a connect button, before this u need to do the port mapping here, the default port number for the server(check the code file terminal_server_sse.py) is 8081, so this port number we need to add in my EC2 Instance bcoz by default this port number is not available.
- For this just click on the security, then go to security groups(we have a url under security groups click on it), then right hand side u wil see "Edit Inbound Rules" click on it, then click on "Add Rules", type: Custom TCP, port range: 8081 and then select: 0.0.0.0/0 that means u can access from anywhere then save the rules.
- Once its done go to the instance again, then click on the id, then on the top right click on "Connect", then on net page also directly click on "Connect".
- It will open the terminal like stuff.

- Here it will give me my EC2 Instance, here we need to install docker as its not already here, we can check the same by using the : docker ps command ---> It will say docker command not found ----> So first we need to install docker here.

## How to install Docker in EC2
- Run the following commands:(In the terminal which has got opened in the EC2 instance)
- sudo apt-get update -y   ---> This will first of all upgrade ur machine as this is newly configured machine. In Linux we have apt its a package manager.
- sudo apt get upgrade
- Install Docker:
- curl -fsSL https://get.docker.com -o get-docker.sh
- sudo sh get-docker .sh
- sudo usermod -aG docker ubuntu
- newgrp docker

- After this now if i write "docker ps" now it will be showing the container id, image and everything.
- To check the docker version: docker --version   ---> U have successfully installed docker on the EC2 instance.

## Deploy MCP SSE Server in AWS Cloud
- For this u need to commit this SSE server of urs onto github.
- Once u have created a repo and commited ur code onto it, then in that repository we go to code(click on it) then under HTTPS we have a url copy it. ---> Basically u have to clone this repository in my EC2 instance.
- Write the command in the EC2 terminal: git clone "paste the copied link" ---> Now it will clone, now to check just write: ls in terminal it will give u the repository name.
- Enter into that repo: cd "repo_name", now if u do ls ---> All the files and folders will be visible ----> In that Dockerfile will also be there. Now i have to build the docker image. (Make ensure u cd into the directory where the dockerfile is actually present).
- We build the docker image there by executing the command: docker build -t terminal_server_sse .
- To check whether the image is built or not just write the command: docker images ----> Here u will be able to see that the image is present.
- Now our image is ready now we will be running this image as a container, so then we execute this command:
- docker run -p 8081:8081 terminal_server_sse
- U can also run in detached mode(-d) that means u can close ur terminal also and still it will be running(ur container).
- Execute: docker ps ----> It will show u that ur container is running ---> It is running on the Port Number 8081
- In ur EC2 instance when u click on the instance id of the instance that u have created there we get something called as IPV4 address. Copy it.
- On the browser paste the IPV4 address and then further add this ---> :8081(this is our port number)
- Now if i hit enter ---> u will be getting Not Found as we haven't added any kind of UI interface.
- Copy this entire thing that u gave in the browser(IPV4 address + port number). ---> This is my server url(we are using this instead of localhost that we have used previously).
- We will be executing our client, the command for that: uv run client_sse.py http://"copied thing"/sse
- The above will be my final command.
- Note: This command u need to execute in ur local pc, in the directory where client_sse.py file is located.
- It(server) will be connected will load all of my tools, mcp client has started, it will be asking us to give us our query in the
terminal.
- Now our MCP server is running as remote that means we have already deployed over the AWS cloud.
- Now even if u close the window still ur MCP server will be running as the container was running in detached mode.

## Terminating the Instance
- Go to instances, select the instance, click on Instance State and then click on "Terminate(delete) instance".