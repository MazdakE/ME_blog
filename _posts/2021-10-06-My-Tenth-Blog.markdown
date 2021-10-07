---
layout: post
title: "Tenth Blog Post"
subtitle: "Lecture 10 - Scaling up and out"
date: 2021-10-06 22:55
categories: jekyll update
---

# Introduction 

In this blog where are going to go throgh the advantages with scaling in the cloud and how easy it is to scale your application, but first we have to make sure that our aplication is able to scale. 

# What is scalability?
The scalability of an application can be measured by the number of requests it can effectively support simultaneously. The point at which an application can no longer handle additional requests effectively is the limit of its scalability. This limit is reached when a critical hardware resource runs out, requiring different or more machines. Scaling these resources can include any combination of adjustments to CPU and physical memory (different or more machines), hard disk (bigger hard drives, less “live” data, solid state drives), and/or the network bandwidth (multiple network interface controllers, fiber and more).

# Scale up vs Scale out

Scaling out, also called horizontal scaling and scaling up, also called vertical scaling. Idea of scaling cloud resources may be intuitive. As your cloud workload changes it's necessary to increase the infrastructure to support increasing load or it may make sense to decrease infrastructure when there is a low demand. The "up and out"-part is perhaps less intuitive. Scaling out is addng more equivalently functional components in prallel to spread out a load. This can go from two load-balanced web server instances to three instrances. Scaling up, in contrast, is making component larger or faster to handle a geat amout of load. This would move our application to a virtual machine with 2 CPU to one with for example 3 CPU. For completeness, scaling down refers to decreasing our system resources, regardless of wheather we were using the up or out approach. 

# Loader.io

I basically followed the steps given [here](https://mikhail.io/2019/07/load-testing-azure-functions-with-loaderio/). I then deployed my changes to Azure. I did a Maintain client load test from 0 client up to 1000 per minute. I then recieved a plot and is shown below.

![Load.io](/ME_blog/images/LoadioPlot.png){:class="img-fluid"}
_Image illustrates how my GET function functions under an interval of time and network bandwidth of 250ms_ 

Ideally our application (which is the blue plot) should behave as the green plot. In this casse it doesn't and fluctualtes alot. But the Average response time is 491ms and we also had 0,1% error rate which explains the massive tilt between 34 and 37 seconds. 

# Cost for scaling up and scaling out

For scaling out we have to increase our instances of our functions and therefore we must scale our App Service in Azure Portal. As a hypotes we say that we want a to upgrade the Core(s) and RAM, since we have alot of storage. So if we for example have the `Standard Tier` for our App Service instance S1, the cost would be $73. If we later need more instances of our functions then a upgrade is necessary for our App Service Instance, and the cost would be double ($146). This is by the way a monthly cost and region I chosed North Europe. 

For scaling up our application we need a unit with larger components that can handle requests much faster. In this case a virtual mashine must be scaled. As a hypotesis, again, if we have an D1 instance and Tier `Standard` with 1 vCPU, 3.5GB RAM and 100GB temp. storage has a cost of $99.22. And if we double our vCPU to 2 with D2 instance, then the cost is almost the double ($188.34).

# Which Tiers for Azure Service App plan gives you the ability to scale

Free and Basic tier does not give you as a customer the ability to scale. For scaling you need to upgrade to either Standard, Basic, or Premium.


### Sources


##### >> [Scaling, Vertical and Horizontal Scaling](https://www.cloudzero.com/blog/horizontal-vs-vertical-scaling)

##### >> [Azure Price calculations](https://azure.microsoft.com/en-us/pricing/calculator/)