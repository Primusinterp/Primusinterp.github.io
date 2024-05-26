---
title: PrimusRedir - Fowarding the world to your C2
categories: [Redteam]
tags: [Red Team, C2, Caddy, Redirector, HTTPS]    
toc: true
---
 

> The tool itself has not been released yet, but its coming very soon!.
{: .prompt-warning }

Time for a new bit of content. While working on the latest update for PrimusC2 i spent quite a while creating an automated redirector infrastructure utilizing a Digital Ocean VPS, Cloudflare, Wireguard and Caddy. This was implemented directly into PrimusC2, but i also turned this into a standalone tool that you can use with any C2: PrimusRedir


## Redirectors in Red Teaming  


Redirectors are one of the most important aspects within C2 infrastructure. Redirectors provide the initial and required obscurity of the C2 team server. 
A redirector functions as a proxy for the team server, the goal is to have one placed in front of every asset in the backend to introduce obscurity and resilience. A basic redirector works in the following way. Implants will call back to the redirectors domain/address, and all the relevant traffic arriving will be redirected to the team server. In Figure one below a setup in its simplest form with one redirector in front of the team server is displayed.

![basic redir](/assets/img/Basic_redirector_setup.jpg)_Figure 1: A basic C2 setup with one redirector_

Multiple types of C2 redirectors exist and they have different strengths and weaknesses. The first redirector type is dump pipe redirection, this means that the redirector will forward any traffic that is directed at a specific listener port, to the team server behind the redirector.  There is no inspection of the traffic, determining if this is a valid callback from the implant or if it’s the blue team probing the redirector to find the obscured C2 server.  A setup like the one mentioned above can be achieved using tools like Socat and IPtables which have the capability to forward traffic from one port to another.  


This setup is the simplest and easiest for a redirector and will provide simple obscurity for the C2 server. A visual representation can be seen in Figure two below.
 
![dump pipe redir](/assets/img/dumb_pipe_redirect.jpg)_Figure 2: Dumb Pipe Redirection with Socat_

The next type is smart pipe redirection, this type will not blindly forward traffic to the C2 server. The traffic will be inspected upon arrival to the redirector, from this point it will determine if the C2 traffic is valid, and then forward the traffic to the C2 server,  if the traffic is invalid, the redirector will either show a fake webpage or it will redirect the traffic to another legitimate site on the internet. This gives a better level of obscurity and enhances the operational security of the infrastructure. When the blue team is fingerprinting and identifying If the current traffic pattern, is leading to an active C2 infrastructure. The mentioned setup, if configured correctly, will hinder the blue team in identifying and exposing the infrastructure and C2 server.  In figure three a visual representation can be observed. 
 
![smart pipe redir](/assets/img/smart_pipe_redirect.jpg)_Figure 3: Smart Pipe Redirection with Caddy_


Depending on which type of C2 channel that is used in the infrastructure, the method of validation can differ from type to type. One of the most popular methods when using the HTTP channel to validate is by using the `user agent` header in the HTTP request. If the correct header isn’t received, the request will be redirected to the fake site. Another example of validation can be the use of JSON Web Token(JWT). The redirector would only accept requests that have a valid and signed JWT in the authorization header, if the token is not valid it would redirect or return a 404-error page. This would require the server to generate a symmetric key that can be used for encryption and decryption of the JWT. The mentioned validation methods are by no means an exhaustive list of methods but are just relevant examples. 


There are multiple options when choosing what kind of system, the redirector should be provisioned on. The first option is a small server or VPS in the cloud. These instances can be provisioned and torn down quickly, this means they are easily automated which is essential for a resilient and efficient C2 infrastructure. When utilizing these VPS/servers several different solutions for handling the redirection exist, some of the popular options for smart pipe redirection are, Apache, Nginx, and lastly Caddy. All options function as reverse proxies and can be configured to use different validation methods.     


The second option is using a serverless approach and using functions from cloud providers such as AWS, Microsoft Azure, and Cloudflare workers. Essentially, it’s just “server-less computing”, this means that you can provide some code to the platform of choice, and it will be executed upon a trigger, that is validated, this could be an SMS, HTTP post request, or anything else that is defined. The cloud provider will then take care of all the computing and resources needed for the code to run. This takes the configuration of servers away from the operators giving them more time to focus on the more important aspects of the infrastructure.  In Figure four a visual representation of an AWS lambda redirector can be seen.
 
![serverless redir](/assets/img/Redirect_cloud_function.jpg)_Figure 4: AWS Lambda Redirector_

All the above are viable solutions in terms of what system and validation type that is chosen. It all depends on what type of engagement that's presented and how the clients environment is configured. However, it’s recommended to employ the smart redirector in most cases, because it will offer improved operational security and give the operator more flexibility in terms of customizing how the redirector behaves.  

With the basics covered i will now introduce my take on a simple, flexible and easy to configure redirector setup using a VPS in the cloud. `PrimusRedir` is a Python script that handles the provisioning, configuration and teardown of a simple redirector in the cloud. In the coming sections i will go into more detail about the components of `PrimusRedir`.

## Overview 
PrimusRedir consists of the following components: 

- Cloudflare for DNS.
- Caddy as the reverse proxy and traffic filtering.
- Wireguard connection between the VPS and the C2 server.
- Digital Ocean VPS
- Terraform as IaC

### Cloudflare 
I selected Cloudflare as the DNS provider due to its advantage of rapid DNS propagation when initializing the redirector. Cloudflare allows you to add your domain as a site prior to the start of the engagement. This provides the operator with the opportunity to alter the nameservers at the domain registrar, ensuring everything is set up and ready before the engagement begins. This process can be carried out continuously, providing the operator with a multitude of domains to use when setting up the redirectors.

It's pretty straight forward to add your domain as site on Cloudflare, just follow the [guide](https://developers.cloudflare.com/fundamentals/setup/manage-domains/add-site/)

> If the DNS settings is set to *proxy* when the DNS record is registered, please change to *DNS only*.
{: .prompt-warning }


*The rest of the content still needs to be written and are currently not ready to be released, once ready it will be added here along with the tool*
### Caddy 

### Wireguard 

### Digital Ocean VPS

### Terraform

![mando](/assets/img/mando.gif)
  