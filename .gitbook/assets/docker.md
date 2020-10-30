# <h1 id="topheading">Docker Information</h1>

## Objectives

Examples of using Docker are here

##  Docker Notes:

**Find Docker Version:**

    docker --version

**List Docker Images:**

    docker images

**Access and execute commands on Docker Image:**

    docker exec -it <container-id> bash

**Remove Docker Container:**

    docker rm <container-id>

**Stop Docker Container:**

    docker stop <container-id>

**Get detailed information about docker installed on the system including the kernel version, number of containers and images, etc.**

    docker info

**Copy file from docker container to local filesystem:**

    sudo docker cp <container-id>:/usr/local/apache2/logs/all.txt /home/all.txt

**Docker History:**

    docker history <image-name>

**Downloading Docker Container Images**

    docker pull <Name-of-Image>

**Getting Container Console with ‘docker attach’:**

    docker attach <Name-of-Image>

**Fetching Logs from the Container:**

    docker logs <container-id>

**To fetch live logs:**

    docker logs -f  <container-id>

**Containers’ resource usage statistics - live streaming:**

    docker stats

**Resource usage statistics without live streaming**

    docker stats --no-stream

**Launching a container with ‘docker run’ command:**

    docker run -it -p 9000:80 --name=debian_container1 debian

**Obtain IP Address of Docker Instance:**

    docker inspect -f "{{.NetworkSettings.IPAddress}}" 806449731d38

**To show only running containers use the given command:**

    docker ps

**To show all containers use the given command:**

    docker ps -a

**To show the latest created container (includes all states) use the given command:**

    docker ps -l

**To show n last created containers (includes all states) use the given command:**

    docker ps -n=-1

**To display total file sizes use the given command:**

    docker ps -s

**Restart Container:**

    docker restart <container-name>
