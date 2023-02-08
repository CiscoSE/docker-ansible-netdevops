# Ansible Container for Cisco NetDevOps
A basic docker container hosting ansible and the required Cisco Collections for automation use cases against Cisco Network infrasturcture.

# Assumptions
The assumption is that the user has familiarity with docker and has docker desktop installed. Alternatively docker can be installed on a linux server to host the development environment.

# Building the Container
Start by either copying the contents of the included Dockerfile or clone the repository to your local machine.

Edit the ansible collections to add or remove any collections to meet the automation needs. Collections can be found on the https://galaxy.ansible.com. 

Once the dockerile is complete run the following command from within the same directory storing the Dockerfile.

```
docker build . -t ansible-netdevops
```
### Tagging and building with specific Ansible Core Versions
It is good practice to tag your containers with versions to denote the differences between containers. An example is assuming building a container with a specific version of ansible eg. 2.12.3 the Dockerfile would be edited by changing the following:

> RUN pip install ansible

To:
> RUN pip install python3 -m pip install --user ansible-core==2.12.3

Then build using the following and tagging the container with the ansible version.

```
docker build . -t ansible-netdevops:2.12.3
```

# Running the Container
To start using the container use the following command to run the container with an interactive session. 

```
docker run --rm -it ansible-netdevops
```
*__NOTE:__ The --rm flag will delete the container upon exiting*

# Using the Container
Running the container as part of the basic `docker run` command will allow access to the environment but provide limited capabilities due to the isolation from the hosts file system. Files would need to be created directly with in the container and would be destroyed once exiting the container. 

An option to this is using Docker Compose and creating a `docker-compose.yml` file in your project directories. Within the `docker-compose.yml` file directives can be defined to share/mount local working directories to the container allowing.

Once the the local directories are mounted to the container use `docker compose up -d` from within the project directory to start a container in detached mode. Then run ansible commands using the `docker exec` command against the instatiated container.

### Example Docker Compose file
Create the following docker-compose.yml file in a project working directory. The compose file defines an ansible service mapping the project working directory to the working home directory in the container.

```
version: "3"
services:
  ansible:
    container_name: ansible
    image: ansible-netdevop
    tty: true
    volumes:
      - ${PWD}:/home:rw
```

To start the container in the project working directory run:
```docker compose up -d```

The files in the working project directory are now available in the container. To execute a command in the container use the exec command, for example:

```
docker exec ansible ansible-galaxy collections list
```
This will list all the installed collections in the container.