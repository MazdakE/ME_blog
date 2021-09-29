---
layout: post
title: "Seventh Blog Post"
subtitle: "Lecture 7 - Cloud Migration"
date: 2021-09-28 22:55
categories: jekyll update
---

# Introduction

In this blog we have been given a scenario based on an application which we want to migrate to the cloud, the thing is that the application contains sensitive data and is merged with internal servers and resources. Due to this our CTO does not concur with PaaS (Platform as a Service) solution since it potentially sending the data over the Internet. An inportant point in modernization it is to implement Azure Service Bus as a message dealer for our enterprise data, but then the CTO disagree since it would unveil messages openly to the Internet.

### What is Vnet

A virtual network (Vnet) is a representation of our own network in the cloud. We will be able to configure our virtual network to communicate directly and securely with our virtual machine. This allows us to send data between our internal servers and the cloud on a private connection.

### Virtual Private Cloud

A virtual private cloud (VPC) service is a representation of your own private cloud network environment on a public shared infrastructure. VPC gives you the ability to jurisdict and describe virtual network that's isolated from public cloud. Data will simply be sent in private with safety. 

Virtual private clouds are implemented by using a security attributes and network functions which gives customers control over to which application or IP address they would like to access.

**Virtual Private Cloud features**

- **Network access control:** There is a network access control list (ACL), an optional layer of security for your VPC that acts as a firewall for controlling traffic that comes in and out. You can set your ACL that acts like your security groups. 

- **Security Groups:** Security groups act as a firewall, controlling both inbound and outbound traffic at the instance level. When you launch an instance, you can associate it with one or more security groups that you've created. Each instance of your VPC could belong to different set of security groups. If you don't set a security group, it will pick the default security group for your VPC.

- **Agility:**  You get access to control the size of your virtual network and deploy cloud resources when your enterprise needs them. You can scale these resources dynamically and in real-time.

![music_get_output](/ME_blog/images/p56761.png){:class="img-fluid"}

_Image illustrates how VPC connected to an on-permisson works between two companies. Image is taken from Alibaba Clouds._

### What is Azure Private Link

A **private endpoint** is a network 'interface' that uses a private IP address from our virtual network. Our 'Private Endpoint' will then connect us privately and securely with the cloud by using a service called 'Azure Private Link'.

This service allows us to access Azure services such as SQL Databases, Azure Storages, etc., through a private endpoint. In this way, we do not have to expose our services or data publicly and can instead keep or send all data privately between us and the cloud.


### Azure serivce bus

A service bus enables us to send data to and guarantee that data arrives. With the help of this service, we can ensure that data is not lost, not duplicated and that it is processed in turn with, for example, FIFO (First-in-First-out). We can use this service when we have set up our private network to connect (and send data) from our internal servers (On-premises) to the cloud. We can also configure our bus and ensure that what we send is guaranteed to be sent "At least once delivery of a message".

### Sources


##### >>[Private endpoint](https://docs.microsoft.com/en-us/azure/event-grid/compare-messaging-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fservice-bus-messaging%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

##### >>[VPC](https://www.ibm.com/cloud/learn/vpc)

##### >>[Azure Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview)

##### >>[Azure Virtual Network](https://docs.microsoft.com/sv-se/azure/virtual-network/virtual-networks-overview)