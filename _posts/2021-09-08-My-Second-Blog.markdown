---
layout: post
title: "Second Blog Post"
subtitle: "Lecture 2 - Continuous Integration"
date: 2021-09-08 22:55
categories: jekyll update
---

# Introduction

In this blog post I'll be talking about CI (Continuous Integration) pipelines. At first I'm going to take you step by step through basics of CI and try to tell you what CI is. Afterwards I will create a Continuous Integration Pipeline using GitHub Actions into an existing project. I'll start by ‘forking’ an existing project from a Github repository. The aim is to make CI Pipeline activate and go through every commit that I have forked. After that I'll try to describe the steps I consider and how I prepared it. I'll also describe the yaml-file in my Github Action step-by-step.

## CI Pipeline, what's that?

Continuous Integration (CI) is usually seen as a test- or approval build server which carefully compiles the code after every commit and tests which has been written in the project and inspects them before deploying the changes to the main branch. Once the code has been approved in parts of build and tests by the CI-pipeline, the new version of the application will be established, also called CD (Continuous Deployment). CI will with the slightest change in the code trigger the pipeline and give a response to the developer/s who have made that specific change. When it passes the CI, it will go to CD and deploy the changes made in the project. CI checks like automated code quality test before releasing the latest code the developer/s has committed. But in order to use a CI Pipeline you have to create its infrastructure first. I will tell you how Ihave built my CI pipeline below.

### Why CI Pipelines and why is it important?

There are alot of benefits with CI pipelines but one of them is that we don't have to go through many lines of codes just to fint the errors or bug that is the cause of why our application doesn't work as we wish to. A developer/s will push some code snippets and those codes will be inspected by the automated code quality pipeline in advance and then being merged into main branch and in time being deployed.

Benefits:

- You don't have to run your test in Visual Studio as an example
- Automated Pipelines discover errors before pushing the code
- Detect bugs or failed tests
- Gives feedback to developer/s to whom code is malfunctioning
- Easier to work agile.

#### References

[CI](https://www.atlassian.com/continuous-delivery/continuous-integration)

[CI-Agile](https://www.scaledagileframework.com/continuous-integration/)

[CI-Pipelines](https://www.redhat.com/en/topics/devops/what-cicd-pipeline)

## How I created my CI Pipeline?

First off I forked an older project called [Spacepark v1](https://github.com/PGBSNH20/spacepark-spaceinvaders) (spacepark-spaceinvaders).
When forking a repository is another word for copying an repository in your own repository, Link: [Forked Spacepark v1](https://github.com/MazdakE/spacepark-spaceinvaders).

After that I created a yaml-file in my forked repository. Before I go through with my explaination, best practice is to create a new branch and work from there so you don't come bump into conflicts.

You start by creating a folder in your branch called .github and after that another folder called workflow, inside workflow you create a file (give it a good name) and end it with the extention .yml.

Next I inserted the logics of what kind of job it should do for us when checking our code.

Follow this link to see the logic that I have inserted: [yaml-file](https://github.com/MazdakE/spacepark-spaceinvaders/blob/main/.github/workflows/ci-test-and-build.yml)

### Step by step explanation of yaml-file

#### Workflows

Workflow is the procedure of the pipeline so that for example more than one job can get triggered by an event. Workflow is used to release, review, package, build and test the project.

#### Events

The event triggers the workflow. When a new commit of codes is inserted, that will trigger the workflow. The workflow will then start to run based on instructions given inside the yaml-file. [yaml-file](https://github.com/MazdakE/spacepark-spaceinvaders/blob/main/.github/workflows/ci-test-and-build.yml).

#### Name-part

You simple name the workflow. Best giving it a good name so you know what it does.

#### Jobs-part

An assignment is simply what we tell our workflow to do. In yaml-file that I have, we see that the first assignment is to build and run on ubuntu-latest `runs-on: ubuntu-latest`. The assignment will in this case execute on a virtual machine that is hosted by GitHub. We tell it to see if we could build our code on `dotnet-version: ['5.0.x']`. These steps are made step-by-step and if one assignment fails the whole thing fails.

#### Runners-part

Instructions that simply listens for available assignments. It runs one assignment once at a time and reports the results. In my case we have runners on different occassions.

#### Steps-part

Here we use `- uses: actions/checkout@v2` to tell the assignment to get version 2 of the action and downloads it to the runner. Next is to setup the .NET verson 5 and IDE .NET Core SDK.
This is where all the steps are grouped together to run where each item is

![ill1](/ME-blog/images/image2.png)

After that we have the stucture of that workflow that it should go through.

![ill2](/ME_blog/images/image3.png)
