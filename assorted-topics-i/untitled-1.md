# Docker

## Objectives

Examples of using Docker are here

## Docker Notes:

**Find Docker Version:**

```text
docker --version
```

**List Docker Images:**

```text
docker images
```

**Access and execute commands on Docker Image:**

```text
docker exec -it <container-id> bash
```

**Remove Docker Container:**

```text
docker rm <container-id>
```

**Stop Docker Container:**

```text
docker stop <container-id>
```

**Get detailed information about docker installed on the system including the kernel version, number of containers and images, etc.**

```text
docker info
```

**Copy file from docker container to local filesystem:**

```text
sudo docker cp <container-id>:/usr/local/apache2/logs/all.txt /home/all.txt
```

**Docker History:**

```text
docker history <image-name>
```

**Downloading Docker Container Images**

```text
docker pull <Name-of-Image>
```

**Getting Container Console with ‘docker attach’:**

```text
docker attach <Name-of-Image>
```

**Fetching Logs from the Container:**

```text
docker logs <container-id>
```

**To fetch live logs:**

```text
docker logs -f  <container-id>
```

**Containers’ resource usage statistics - live streaming:**

```text
docker stats
```

**Resource usage statistics without live streaming**

```text
docker stats --no-stream
```

**Launching a container with ‘docker run’ command:**

```text
docker run -it -p 9000:80 --name=debian_container1 debian
```

**Obtain IP Address of Docker Instance:**

```text
docker inspect -f "{{.NetworkSettings.IPAddress}}" 806449731d38
```

**To show only running containers use the given command:**

```text
docker ps
```

**To show all containers use the given command:**

```text
docker ps -a
```

**To show the latest created container \(includes all states\) use the given command:**

```text
docker ps -l
```

**To show n last created containers \(includes all states\) use the given command:**

```text
docker ps -n=-1
```

**To display total file sizes use the given command:**

```text
docker ps -s
```

**Restart Container:**

```text
docker restart <container-name>
```

