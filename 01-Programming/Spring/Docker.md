# Dockerizing and application

## Important concepts:

### Docker image
A Docker image is a read-only template used to create Docker containers. An image basically contains everything needed to create a container, like application files
required libraries, dependencies, configuration and instructions for what should run. The image itself does not run, it can be seen as a blueprint to create a container.

### Docker container
A Docker container is a running, isolated instance of a Docker image. It's what actually executes the application. An image can be used to create multiple containers. Each container is an isolated environment with its own processes, filesystem view, network configuration, etc.
The important relationship is:

Image = template
Container = running instance of that template

### Dockerfile
A Dockerfile is a text file containing instructions for building a Docker image.
#### Relationship:
Dockerfile → Image → Container
