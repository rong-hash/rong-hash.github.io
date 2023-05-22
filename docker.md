# Docker Usage

**Docker Command**

- `docker pull [image name]`: Download an image from the Docker Hub.

- `docker run [image name]`: Create and start a container from an image.

- `docker ps`: Show running containers.

- `docker ps -a`: Show all containers (both running and stopped).

- `docker stop [container ID or name]`: Stop a running container.

- `docker start [container ID or name]`: Start a stopped container.

- `docker rm [container ID or name]`: Remove a container.

- `docker rmi [image name]`: Remove an image.

- `docker images`: List all images.

- `docker exec -it [container ID or name] [command]`: Execute a command in a running container. 

- `docker logs [container ID or name]`: View the logs of a container.

- `docker build -t [image name] .`: Build a Docker image from a Dockerfile in the current directory.

- `docker run -d [image name]`: Run a container in the background (detached mode).

- `docker run -p [host port]:[container port] [image name]`: Map a port in the container to a port on the host.

- `docker run -v [host directory]:[container directory] [image name]`: Mount a volume from the host into the container.

- `docker inspect [container ID or name]`: Get detailed information about a container.

- `docker network ls`: List networks.

Remember that you can append `--help` to any Docker command to get more information about its usage and options.

These are just the basics. Docker has a wide range of commands and options that you can use to manage images, containers, networks, and volumes, as well as to configure Docker itself. You can find more detailed information in the Docker documentation.