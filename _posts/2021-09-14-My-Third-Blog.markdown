---
layout: post
title: "Third Blog Post"
subtitle: "Lecture 3 - Docker"
date: 2021-09-14 22:55
categories: jekyll update
---

# Introduction

In this blog today will be about Docker on how to use it with containers and images. We first off are going to fork this repo: [Simple Hello World app](https://github.com/skjohansen/SimpleWebHalloWorld) and build a container based on this repo and run it. The second exercise is about working with Docker-Compose on the repo that we have forked (even if we don't have much use for it because we do not have multiple containers to run at once). On the other hand it is good to know in case you work on a bigger project and need to open many containers at once. Last exercise (exercise 3) is about building a Github Pipeline for this forked repo and publish this application to Github Registry. When it's done we are able to pull our created image with `docker-pull`.

## Exercise 1 - Creating a Dockerfile for our repo

This exercise told us to fork a repository as mentioned below. I forked it to m repo: [SimpleWebHalloWorld](https://github.com/MazdakE/SimpleWebHalloWorld).
First thing was to write a Dockerfile (case sensitive). I used Visual Studio Code to solve this exercise and there are two ways to solve this: either right click in Explorer and click new file and name it or open terminal and write {% highlight javascript %}code Dockerfile{% endhighlight %}

Inside the Dockerfile I wrote the image so that I could create a Docker Container. {% highlight  csharp%}
FROM mcr.microsoft.com/dotnet/aspnet:3.1 AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:3.1 AS build
WORKDIR /src
COPY ["SimpleWebHalloWorld.csproj", "."]
RUN dotnet restore "./SimpleWebHalloWorld.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "SimpleWebHalloWorld.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "SimpleWebHalloWorld.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "SimpleWebHalloWorld.dll"]
{% endhighlight %}

So this is probably what Visual Studio could have created but got the information from [Docker and .NET Core 101](https://www.youtube.com/watch?v=vmnvOITMoIg&list=PLdo4fOcmZ0oUvXP_Pt2zOgk8dTWagGs_P) in order to create my Docker Image.

### Explaining Dockerfile

In Dockerfile we see `FROM mcr.microsoft.com/dotnet/sdk:3.1 AS build` which means it will basically build `aspnet 3.1` as base image. We have `WORKDIR` to simplify so that we don't have to write `/app` when we use for example `CMD` to run our application. In other words it locates directly to `/app` folder. `EXPOSE` exposes the ports, in this case port 80.

Next `FROM mcr.microsoft.com/dotnet/sdk:3.1 AS build` is the build part that we use to build. So `FROM` is very important to when we want to create a container so that when someone who doesn't have these versions or the program can open up without downloading many things and put a alot of time in installing. With `build` in the Dockerfile we build our project by copying it to the container and run it.
Publish `RUN dotnet publish "SimpleWebHalloWorld.csproj" -c Release -o /app/publish` simly takes what has been built.
Last part
{% highlight  csharp%}
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "SimpleWebHalloWorld.dll"]
{% endhighlight %}
takes the image and the project together, basically everything I need to be able to run the application. I recommend you to watch [Docker and .NET Core 101](https://www.youtube.com/watch?v=vmnvOITMoIg&list=PLdo4fOcmZ0oUvXP_Pt2zOgk8dTWagGs_P) to fully understand.

### How I run this Docker Image inside of a container?

So first of all i started terminal in Visual Studio Code so that I can skip using `cd` to come in my working directory.

In my working directory I tell the created Dockerfile to build that image by typing {% highlight  csharp%}docker build -t simplehelloworld .{% endhighlight %}`-t` is the tag you name your build and `.` takes everything in that directory.

If no errors, the image should be built. To see if the image is running there are two ways, write `docker images` or `docker image ls` in command to see your built image.

To delete your docker image, type: `docker rmi *(unique image id)*`

Now that our image is running, next is to run it inside the container. By typing: `docker run -d -p 8080:80 simplehelloworld` in command.
This command runs the image inside a container with that application. `-d` tells to run the container in detached mode. `-p 8080:80` says that the project should run on port 8080, from port 80 in Docker. Now I can access the `http://localhost:8080`.

## GitHub Pipeline publishing Docker Image with GitHub Registry

So here I created a pipeline so everytime I build my Dockerfile, and if it passes all steps requiered it will push to Github registry so that I can pull the image with `docker-pull` command.

This is the pipeline that I have created:
{% highlight csharp%}
name: Build-Push Image Pipeline Docker

on: [push]

jobs:
docker:
runs-on: ubuntu-latest
steps: - uses: actions/checkout@v2 - name: Set up QEMU
uses: docker/setup-qemu-action@v1 - name: Set up Docker Buildx
uses: docker/setup-buildx-action@v1 - name: Login to GitHub Container Registry #DockerHub #GitHub Container Registry
uses: docker/login-action@v1
with:
registry: ghcr.io
username: ${{ github.actor }}
password: ${{ secrets.CR_PAT }} - name: Build and push
id: docker_build
uses: docker/build-push-action@v2
with:
push: true
context: ${{ env.working-directory }}
tags: |
ghcr.io/mazdake/simplewebhalloworld:master
{% endhighlight %}

Lets have a look at the pipeline.

I will not go through every step in the pipeline since I have talked about it in my previous blog.
What this pipeline is doing is that it triggers every commit made `on: [push] ` which this explains.
The pipeline builds an application which we do first `- uses: actions/checkout@v2`. `QEMU` stands for QemuIoTests and is good to use when building against platforms and `Buildx` action will create and boot a builder using [drivers for docker-containers](https://github.com/docker/buildx/blob/master/docs/reference/buildx_create.md#driver). Next step is to log in to Github, go to your personal settings,
This pipeline will, build the application which we will do in the first step`- uses: actions/checkout@v2`. The `QEMU` action is usefull if we want to build against more platforms and the `Buildx` action will create and boot a builder by using [docker-container builder driver](https://github.com/docker/buildx/blob/master/docs/reference/buildx_create.md#driver). Next step get the token from GitHub Container Registry (ghcr.io) where we explicitly tell our pipeline to use our github username as username and a secret token as password. If you want to know how to use a token and why it's used go to --> [GitHub Tokens and Secrets](https://itnext.io/build-ship-github-container-registry-kubernetes-aa06029b3f21#0075).

I got in to GitHub account -> Settings -> Developer Settings -> `Personal Access Tokens`. Once I navigated to `Personal Access Tokens` I generated a new token. The next step was to generate a `Secret` inside my repository.

In my repository I got in to `Secrets`. Created a `New repository secret` and named it. In our case we have this line of code: `password: secrets.CR_PAT`. That means that our secret should be named `CR_PAT`. Right after we have named the secret, it is time to paste my `Personal Access Token` into the value field, which we created and received in the first step. Great, we are now set and have a secret which our YAML File can read from. This will also be our credentials when we sign into ghcr.io inside of our pipeline. If every step inside of our `jobs:` succeeds then our pipeline will build and push this image into GitHub Registry.

So, how do we know that our image has been pushed into the registry? Well, we can always check that in our GitHub profile.

As a last step, lets have a look at how it is to pull this image based on the information in the image above. So what we will do is to execute the command (in this case inside of CMD) and see inside Docker Desktop if the image was pulled.

### Conclusion

A list of what we've been covering in this particular blog post.

- How to create a Dockerfile
- How to build/create an Image
- How to run a container
- How to build a Pipeline that pushed our Docker Image into GitHub Registry
- How to generate tokens and secrets on a particular repository

#### References

[What is Docker](https://www.youtube.com/watch?v=vmnvOITMoIg&list=PLdo4fOcmZ0oUvXP_Pt2zOgk8dTWagGs_P)

[Docker Containers](https://codemag.com/Article/2103061/Introduction-to-Containerization-Using-Docker)

[Docker Pipeline](https://github.com/docker/build-push-action)

[Secrets & Tokens](https://itnext.io/build-ship-github-container-registry-kubernetes-aa06029b3f21#0075)
