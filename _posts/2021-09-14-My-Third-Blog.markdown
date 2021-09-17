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

Inside the Dockerfile I wrote the image so that I could create a Docker Container. <pre class="yaml" style="font-family:monospace;">FROM mcr.microsoft.com/dotnet/aspnet:3.1 AS base
WORKDIR /app
EXPOSE 80
&nbsp;
FROM mcr.microsoft.com/dotnet/sdk:3.1 AS build
WORKDIR /src
COPY <span class="br0">&#91;</span><span style="color: #CF00CF;">&quot;SimpleWebHalloWorld.csproj&quot;</span>, <span style="color: #CF00CF;">&quot;.&quot;</span><span class="br0">&#93;</span>
RUN dotnet restore <span style="color: #CF00CF;">&quot;./SimpleWebHalloWorld.csproj&quot;</span>
COPY . .
WORKDIR <span style="color: #CF00CF;">&quot;/src/.&quot;</span>
RUN dotnet build <span style="color: #CF00CF;">&quot;SimpleWebHalloWorld.csproj&quot;</span> -c Release -o /app/build
&nbsp;
FROM build AS publish
RUN dotnet publish <span style="color: #CF00CF;">&quot;SimpleWebHalloWorld.csproj&quot;</span> -c Release -o /app/publish
&nbsp;
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT <span class="br0">&#91;</span><span style="color: #CF00CF;">&quot;dotnet&quot;</span>, <span style="color: #CF00CF;">&quot;SimpleWebHalloWorld.dll&quot;</span><span class="br0">&#93;</span></pre>

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

<pre class="yaml" style="font-family:monospace;"><ol><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">name</span><span style="font-weight: bold; color: brown;">: </span>Build-Push Image Pipeline Docker</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">on</span><span style="font-weight: bold; color: brown;">: </span><span class="br0">&#91;</span>push<span class="br0">&#93;</span></div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li><li style="font-weight: bold; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #007F45;">jobs</span>:</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #007F45;">&nbsp; docker</span>:</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;   runs-on</span><span style="font-weight: bold; color: brown;">: </span>ubuntu-latest</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #007F45;">&nbsp;   steps</span>:</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;     - uses</span><span style="font-weight: bold; color: brown;">: </span>actions/checkout@v2</div></li><li style="font-weight: bold; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;     - name</span><span style="font-weight: bold; color: brown;">: </span>Set up QEMU</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;       uses</span><span style="font-weight: bold; color: brown;">: </span>docker/setup-qemu-action@v1</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;     - name</span><span style="font-weight: bold; color: brown;">: </span>Set up Docker Buildx</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;       uses</span><span style="font-weight: bold; color: brown;">: </span>docker/setup-buildx-action@v1</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;     - name</span><span style="font-weight: bold; color: brown;">: </span>Login to GitHub Container Registry <span style="color: blue;">#DockerHub #GitHub Container Registry</span></div></li><li style="font-weight: bold; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;       uses</span><span style="font-weight: bold; color: brown;">: </span>docker/login-action@v1</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #007F45;">&nbsp;       with</span>:</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;         registry</span><span style="font-weight: bold; color: brown;">: </span>ghcr.io</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;         username</span><span style="font-weight: bold; color: brown;">: </span>$<span class="br0">&#123;</span><span class="br0">&#123;</span> github.actor <span class="br0">&#125;</span><span class="br0">&#125;</span></div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;         password</span><span style="font-weight: bold; color: brown;">: </span>$<span class="br0">&#123;</span><span class="br0">&#123;</span> secrets.CR_PAT <span class="br0">&#125;</span><span class="br0">&#125;</span></div></li><li style="font-weight: bold; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;     - name</span><span style="font-weight: bold; color: brown;">: </span>Build and push</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;       id</span><span style="font-weight: bold; color: brown;">: </span>docker_build</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;       uses</span><span style="font-weight: bold; color: brown;">: </span>docker/build-push-action@v2</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #007F45;">&nbsp;       with</span>:</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;         push</span><span style="font-weight: bold; color: brown;">: </span>true</div></li><li style="font-weight: bold; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;         context</span><span style="font-weight: bold; color: brown;">: </span>$<span class="br0">&#123;</span><span class="br0">&#123;</span> env.working-directory <span class="br0">&#125;</span><span class="br0">&#125;</span></div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: green;">&nbsp;         tags</span><span style="font-weight: bold; color: brown;">: |</div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"></span><span style="color: #303050;background-color: #F5F5F5">            ghcr.io/mazdake/simplewebhalloworld:master</span></div></li><li style="font-weight: normal; vertical-align:top;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li></ol></pre>

Lets have a look at the pipeline.

I will not go through every step in the pipeline since I have talked about it in my previous blog.
What this pipeline is doing is that it triggers every commit made `on: [push] ` which this explains.
The pipeline builds an application which we do first `- uses: actions/checkout@v2`. `QEMU` stands for QemuIoTests and is good to use when building against platforms and `Buildx` action will create and boot a builder using [drivers for docker-containers](https://github.com/docker/buildx/blob/master/docs/reference/buildx_create.md#driver). Next step get the token from GitHub Container Registry (ghcr.io) where we explicitly tell our pipeline to use our github username as username and a secret token as password. If you want to know how to use a token and why it's used go to --> [GitHub Tokens and Secrets](https://itnext.io/build-ship-github-container-registry-kubernetes-aa06029b3f21#0075).

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
