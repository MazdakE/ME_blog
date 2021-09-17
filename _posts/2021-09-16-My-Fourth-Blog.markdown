---
layout: post
title: "Fourth Blog Post"
subtitle: "Lecture 4 - Azure"
date: 2021-09-16 22:55
categories: jekyll update
---

# Introduction

In this blog we will be going through some aspects of what Serverless and Function As A Service (FaaS) is. After that I'll be illustrating how we made our calculations app run in Azure functions with screenshots, scrips and how our pipelines work before implementing the changes from Github to Azure. Lastly we will take a look on how we tested our application and bring up some security hazards with the application that we just have created.

## What is Serverless and Function As A Service (FaaS)

**Serverless** is a cloud-based execution model that lets you focus more on your code and less on the server management such as pipelines for example. You basically put your set of function files and let the host take care of it for you. Here are some good tips on [serverless](https://www.youtube.com/watch?v=Fx3ZGy-mbV4) that might help you understand how it works. Common use is like when a client goes to your contact page and sends the form which sends to a serverless function, and that serverless function forward it to the host email.

On the other hand **FaaS** or **function as a service is basically** a cloud service which lets developers to execute code without physical hardware.
Many cloud-services provide FaaS and is one of the simpler levels of cloud computing. Common use cases include sending an email every couple of days to, for example remind user to pay its monthly fee.

## Our Calculations app

So the way we created our calculator app was by starting Visual Studio and creating a new project with C# as language and Azure as OS and the type of project we created av Azure Functions, see image below:

![Azure Project](/ME_blog/images/azure_project){:width="600px"}

Here you see the calculator that we built, don't forget to scroll to the right to see the whole code:

<!-- From https://highlight.hohli.com/index.ph -->
<pre class="csharp" style="font-family:monospace;"><span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.IO</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.Threading.Tasks</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.AspNetCore.Mvc</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.Azure.WebJobs</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.Azure.WebJobs.Extensions.Http</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.AspNetCore.Http</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.Extensions.Logging</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Newtonsoft.Json</span><span style="color: #008000;">;</span>
&nbsp;
<span style="color: #0600FF; font-weight: bold;">namespace</span> CalculatorApp
<span style="color: #008000;">&#123;</span>
    <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #0600FF; font-weight: bold;">static</span> <span style="color: #6666cc; font-weight: bold;">class</span> Function1
    <span style="color: #008000;">&#123;</span>
        <span style="color: #008000;">&#91;</span>FunctionName<span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;CalculatorApplication&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#93;</span>
        <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #0600FF; font-weight: bold;">static</span> <span style="color: #0600FF; font-weight: bold;">async</span> Task<span style="color: #008000;">&lt;</span>IActionResult<span style="color: #008000;">&gt;</span> Run<span style="color: #008000;">&#40;</span>
            <span style="color: #008000;">&#91;</span>HttpTrigger<span style="color: #008000;">&#40;</span>AuthorizationLevel<span style="color: #008000;">.</span><span style="color: #0000FF;">Function</span>, <span style="color: #666666;">&quot;get&quot;</span>, <span style="color: #666666;">&quot;post&quot;</span>, Route <span style="color: #008000;">=</span> <span style="color: #0600FF; font-weight: bold;">null</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#93;</span> HttpRequest req,
            ILogger log<span style="color: #008000;">&#41;</span>
        <span style="color: #008000;">&#123;</span>
            <span style="color: #0600FF; font-weight: bold;">try</span>
            <span style="color: #008000;">&#123;</span>
                log<span style="color: #008000;">.</span><span style="color: #0000FF;">LogInformation</span><span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;C# HTTP trigger function processed a request.&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
&nbsp;
                <span style="color: #6666cc; font-weight: bold;">double</span> value1 <span style="color: #008000;">=</span> <span style="color: #FF0000;">0</span>, value2 <span style="color: #008000;">=</span> <span style="color: #FF0000;">0</span><span style="color: #008000;">;</span>
&nbsp;
                value1 <span style="color: #008000;">=</span> Convert<span style="color: #008000;">.</span><span style="color: #0000FF;">ToDouble</span><span style="color: #008000;">&#40;</span>req<span style="color: #008000;">.</span><span style="color: #0000FF;">Query</span><span style="color: #008000;">&#91;</span><span style="color: #666666;">&quot;value1&quot;</span><span style="color: #008000;">&#93;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
                value2 <span style="color: #008000;">=</span> Convert<span style="color: #008000;">.</span><span style="color: #0000FF;">ToDouble</span><span style="color: #008000;">&#40;</span>req<span style="color: #008000;">.</span><span style="color: #0000FF;">Query</span><span style="color: #008000;">&#91;</span><span style="color: #666666;">&quot;value2&quot;</span><span style="color: #008000;">&#93;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
&nbsp;
                <span style="color: #0600FF; font-weight: bold;">var</span> result <span style="color: #008000;">=</span> value1 <span style="color: #008000;">+</span> value2<span style="color: #008000;">;</span>
&nbsp;
                <span style="color: #6666cc; font-weight: bold;">string</span> responseMessage <span style="color: #008000;">=</span> $<span style="color: #666666;">&quot;value1: {value1}<span style="color: #008080; font-weight: bold;">\n</span>value2: {value2}<span style="color: #008080; font-weight: bold;">\n</span>Your result is {value1} + {value2} = {result}&quot;</span><span style="color: #008000;">;</span>
&nbsp;
                <span style="color: #0600FF; font-weight: bold;">return</span> <span style="color: #008000;">new</span> OkObjectResult<span style="color: #008000;">&#40;</span>responseMessage<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            <span style="color: #008000;">&#125;</span>
            <span style="color: #0600FF; font-weight: bold;">catch</span>
            <span style="color: #008000;">&#123;</span>
                <span style="color: #0600FF; font-weight: bold;">return</span> <span style="color: #008000;">new</span> BadRequestObjectResult<span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;Your calculations are invalid. Try again!&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            <span style="color: #008000;">&#125;</span>
&nbsp;
        <span style="color: #008000;">&#125;</span>
    <span style="color: #008000;">&#125;</span>
<span style="color: #008000;">&#125;</span>
&nbsp;</pre>

So what this calculator contains is a run-method that takes two parameters, one is Http Request which is called `req` and has an attribute in front of it that says the values passed as in post and get request. ILogger is just athorization which we will not focus on in this exercise.

Next is that we take information about value1 and value2 from the HttpRequest and since they come as type string we have to convert them to double. After that we sum the value and if there is no exceptions we will get an Ok request and the sum will be shown in that page. Otherwise we will get an exception and an message will pop up in the page.

Just a preview on how the site looks like, shown below:

The URL to our calculator site is `https://additionalcalculator.azurewebsites.net/api/CalculatorApplication?code=gWXblWD6oafWJMf1Xtv3E6G9WRFmJPPetpRDWuVphOrdhjgzAlk5pQ==` and as we add `&value1=19&value2=21` to our URL we get the sum of those values that we just inserted.

![Calculation Successful](/ME_blog/images/azure_success.png){:width="600px"}

And if it fails an message will bve shown informing the client:

![Calculation failed](/ME_blog/images/azure_failed.png){:width="600px"}

## For Silver inermediate exercise

We followed the steps in how to [Create a function app in Azure that is deployed from GitHub](https://docs.microsoft.com/en-us/azure/azure-functions/scripts/functions-cli-create-function-app-github-continuous).

![Azure Github Instructions](/ME_blog/images/azure_github_silver.png){:width="600px"}

So we wrote down these CLI commands from powershell and also created a git token so that we could deploy app in our repo where Azure Portal takes it from.

## Testing

Testing it was really simple. There are many ways of testing the calculation that we created and one of those ways did I mention earlier. Another way of testing the calculation is by going in to your Azure Portal-> (your function) -> Test/Run as I will show below:

![Function test](/ME_blog/images/testing_function.png){:width="600px"}

As you can see we use Http Post, key Master(host), and the values as queries. We remove exerything there is in body. After that we click run and see what the result is.

![Result success](/ME_blog/images/result_success.png){:width="600px"}

In this case we got `http result 200 OK` and the result is shown.

## Gold exercise

In gold exercise we followed [Using Publish Profile as Deployment Credential (recommended) and Using Azure Service Principal for RBAC as Deployment Credential](https://github.com/marketplace/actions/azure-functions-action) in order to make a pipeline so that the changes applies to that function in Azure Portal. The workflow that we used was [this](https://github.com/Azure/actions-workflow-samples/blob/master/FunctionApp/windows-dotnet-functionapp-on-azure-rbac.yml) and you can also find our workflow with some changes in order to make it work with our function and so that it has the right dotnet version.

<pre class="yaml" style="font-family:monospace;"><span style="color: green;">name</span><span style="font-weight: bold; color: brown;">: </span>Windows_Dotnet_Workflow
<span style="color: #007F45;">
on</span><span style="font-weight: bold; color: brown;">:
</span>  <span class="br0">&#91;</span>push<span class="br0">&#93;</span>
&nbsp;
<span style="color: blue;"># CONFIGURATION</span>
<span style="color: blue;"># For help, go to https://github.com/Azure/Actions</span>
<span style="color: blue;">#</span>
<span style="color: blue;"># 1. Paste the RBAC json into the following secret in your repository:</span>
<span style="color: blue;">#   AZURE_RBAC_CREDENTIALS</span>
<span style="color: blue;">#</span>
<span style="color: blue;"># 2. Change these variables for your configuration:</span><span style="color: #007F45;">
env</span>:<span style="color: green;">
  AZURE_FUNCTIONAPP_NAME</span><span style="font-weight: bold; color: brown;">: </span>AdditionalCalculator  <span style="color: blue;"># set this to your application's name</span><span style="color: green;">
  AZURE_FUNCTIONAPP_PACKAGE_PATH</span><span style="font-weight: bold; color: brown;">: </span>'.'    <span style="color: blue;"># set this to the path to your web app project, defaults to the repository root</span><span style="color: green;">
  DOTNET_VERSION</span><span style="font-weight: bold; color: brown;">: </span>'3.1'              <span style="color: blue;"># set this to the dotnet version to use</span>
<span style="color: #007F45;">
jobs</span>:<span style="color: #007F45;">
  build-and-deploy</span>:<span style="color: green;">
    runs-on</span><span style="font-weight: bold; color: brown;">: </span>windows-latest<span style="color: green;">
    environment</span><span style="font-weight: bold; color: brown;">: </span>dev<span style="color: #007F45;">
    steps</span>:<span style="color: green;">
    - name</span><span style="font-weight: bold; color: brown;">: </span>'Checkout GitHub Action'<span style="color: green;">
      uses</span><span style="font-weight: bold; color: brown;">: </span>actions/checkout@master
<span style="color: green;">
    - name</span><span style="font-weight: bold; color: brown;">: </span>'Login via Azure CLI'<span style="color: green;">
      uses</span><span style="font-weight: bold; color: brown;">: </span>azure/login@v1<span style="color: #007F45;">
      with</span>:<span style="color: green;">
        creds</span><span style="font-weight: bold; color: brown;">: </span>$<span class="br0">&#123;</span><span class="br0">&#123;</span> secrets.AZURE_RBAC_CREDENTIALS <span class="br0">&#125;</span><span class="br0">&#125;</span>
<span style="color: green;">
    - name</span><span style="font-weight: bold; color: brown;">: </span>Setup DotNet $<span class="br0">&#123;</span><span class="br0">&#123;</span> env.DOTNET_VERSION <span class="br0">&#125;</span><span class="br0">&#125;</span> Environment<span style="color: green;">
      uses</span><span style="font-weight: bold; color: brown;">: </span>actions/setup-dotnet@v1<span style="color: #007F45;">
      with</span>:<span style="color: green;">
        dotnet-version</span><span style="font-weight: bold; color: brown;">: </span>$<span class="br0">&#123;</span><span class="br0">&#123;</span> env.DOTNET_VERSION <span class="br0">&#125;</span><span class="br0">&#125;</span>
<span style="color: green;">
    - name</span><span style="font-weight: bold; color: brown;">: </span>'Run dotnet'<span style="color: green;">
      shell</span><span style="font-weight: bold; color: brown;">: </span>pwsh<span style="color: green;">
      run</span><span style="font-weight: bold; color: brown;">: |
</span><span style="color: #303050;background-color: #F5F5F5">        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd</span><span style="color: green;">
    - name</span><span style="font-weight: bold; color: brown;">: </span>'Run Azure Functions Action'<span style="color: green;">
      uses</span><span style="font-weight: bold; color: brown;">: </span>Azure/functions-action@v1<span style="color: green;">
      id</span><span style="font-weight: bold; color: brown;">: </span>fa<span style="color: #007F45;">
      with</span>:<span style="color: green;">
        app-name</span><span style="font-weight: bold; color: brown;">: </span>$<span class="br0">&#123;</span><span class="br0">&#123;</span> env.AZURE_FUNCTIONAPP_NAME <span class="br0">&#125;</span><span class="br0">&#125;</span><span style="color: green;">
        package</span><span style="font-weight: bold; color: brown;">: </span>'$<span class="br0">&#123;</span><span class="br0">&#123;</span> env.AZURE_FUNCTIONAPP_PACKAGE_PATH <span class="br0">&#125;</span><span class="br0">&#125;</span>/output'
&nbsp;
<span style="color: blue;"># For more samples to get started with GitHub Action workflows to deploy to Azure, refer to https://github.com/Azure/actions-workflow-samples</span></pre>

Important details that should not be leaked is in our secrets in our Github.

## Security

When you deal with cloud there are some things you need to take into consideration, and of them is security. There are no guarantee that you are secure to 100% against hackers. But there are some top priorities in order to make an secure enviroment for your functions and data:

Authentication is one way to secure. You should require authentication by using some sort of API keys or AntiForgeryTokens so that you and your client can feel safe. This we reduce unwanted traffic but also reduce other security risks.

Write some security code. As a developer you can't only rely on a simple log in security and need to think from all aspects. An example can be SQL Injections, function on runtime code and many more.

## Conclusion

This concludes first a simple introduction to Serverless and FaaS. We also did a FaaS of our own and also deployed our function app from Github. Lastly we created a workflow pipeline when we push and deploy from Github to Azure Portal.

## References

- [FaaS](https://www.ibm.com/cloud/learn/faas)
- [Learn models](https://docs.microsoft.com/en-us/learn/modules/choose-azure-service-to-integrate-and-automate-business-processes/)
- [Serverless](https://docs.microsoft.com/en-us/learn/modules/create-serverless-logic-with-azure-functions/)
- [Azure deployed from Github](https://docs.microsoft.com/en-us/azure/azure-functions/scripts/functions-cli-create-function-app-github-continuous)
- [Triggers](https://docs.microsoft.com/en-us/learn/modules/execute-azure-function-with-triggers/)
- [Security](https://we45.com/blog/top-10-security-risks-in-serverless/)
