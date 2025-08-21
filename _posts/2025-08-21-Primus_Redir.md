---
title: PrimusRedir - Fowarding the world to your C2
categories: [Redteam]
tags: [Red Team, C2, Caddy, Redirector, HTTPS]    
toc: true
---
 



Time for a new bit of content. While working on the latest update for PrimusC2 i spent quite a while creating an automated redirector infrastructure utilizing a Digital Ocean VPS, Cloudflare, Wireguard and Caddy. This was implemented directly into PrimusC2, but i also turned this into a standalone tool that you can use with any C2: [PrimusRedir](https://github.com/Primusinterp/PrimusRedir)

This was done in order to have a standalone tool that could be used to spin up the needed infrastructure easy and quickly for a wide range of C2 frameworks. The following sections provide an introduction to redirectors and how they work, while the final section covers details about PrimusRedir.


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

## Overview of [PrimusRedir](https://github.com/Primusinterp/PrimusRedir)
PrimusRedir consists of the following components: 

- Cloudflare for DNS.
- Caddy as the reverse proxy and traffic filtering.
- Wireguard connection between the VPS and the C2 server.
- Digital Ocean VPS
- Terraform as IaC

With just a few commands, you will have a redirector up and running for your C2.

### Cloudflare 
I selected Cloudflare as the DNS provider due to its advantage of rapid DNS propagation when initializing the redirector. Cloudflare allows you to add your domain as a site prior to the start of the engagement. This provides the operator with the opportunity to alter the nameservers at the domain registrar, ensuring everything is set up and ready before the engagement begins. This process can be carried out continuously, providing the operator with a multitude of domains to use when setting up the redirectors.

It's pretty straight forward to add your domain as site on Cloudflare, just follow the [guide](https://developers.cloudflare.com/fundamentals/setup/manage-domains/add-site/)

> If the DNS settings is set to *proxy* when the DNS record is registered, please change to *DNS only*.
{: .prompt-warning }

#### Setting Up Cloudflare API Token

To use PrimusRedir, you'll need to create a Cloudflare API token with the correct permissions. Follow these steps:

1. **Add Your Domain to Cloudflare**
   - Go to [Cloudflare Dashboard](https://dash.cloudflare.com)
   - Click "Add a Site" and enter your domain
   - Choose the Free plan
   - Delete all existing DNS entries (we'll add them later)
   - Copy the two Cloudflare nameservers provided
   - Go to your domain registrar and update the nameservers to the Cloudflare ones

2. **Create API Token**
   - Navigate to [API Tokens](https://dash.cloudflare.com/profile/api-tokens)
   - Click "Create Token" → "Create Custom Token"
   - Use the following settings:

   **Token Name:** `PrimusRedir DNS Token` (or any descriptive name)
   
   **Permissions:**
   - Zone → DNS → Edit
   - Zone → Zone → Edit
   
   **Zone Resources:**
   - Include → All zones (or specific zones if you prefer)
   
   **Client IP Address Filtering:**
   - Leave as default (all addresses) or restrict to your IP range
   
   **TTL:**
   - Set an appropriate expiration date (recommend 6-12 months)

3. **Copy and Store Token**
   - After creating the token, copy the generated API key
   - Store it securely - you won't be able to see it again
   - Use this token when running `python3 Primus_redir.py --config`

> **Important:** The API token needs both Zone and DNS edit permissions to create and manage DNS records automatically. Without these permissions, PrimusRedir will fail to provision the redirector.
{: .prompt-warning }

Once configured, PrimusRedir will automatically handle DNS record creation and management for your redirector domains.

### Caddy 

Caddy serves as the reverse proxy and traffic filtering component in PrimusRedir. It's chosen for its simplicity, automatic SSL/TLS certificate management via Let's Encrypt. The Caddy configuration implements smart pipe redirection by:

1. **User-Agent Validation**: Only allowing traffic with specific User-Agent strings (configurable during setup)
2. **Traffic Filtering**: Blocking known malicious IPs and User-Agents from security scanners, bots, and crawlers
3. **Legitimate Traffic Redirection**: Forwarding valid C2 traffic to the backend C2 server
4. **Decoy Responses**: Redirecting invalid traffic to legitimate websites or showing fake "under construction" pages

The Caddy configuration automatically handles SSL/TLS termination, ensuring all external communication is encrypted while maintaining the internal HTTP communication between Caddy and the C2 server.


### Wireguard 

Wireguard provides the secure tunnel between the operator's machine and the VPS redirector. This creates a private network (192.168.255.0/24) where:

- The VPS acts as the Wireguard server (192.168.255.1)
- The operator's machine becomes a Wireguard client (192.168.255.2)
- All C2 traffic flows through this encrypted tunnel
- The tunnel is automatically configured and started during provisioning



### Digital Ocean VPS

Digital Ocean provides the cloud infrastructure for the redirector. PrimusRedir automatically:

- Provisions a new VPS instance
- Configures the operating system with necessary packages
- Sets up Docker and Caddy containers
- Establishes the Wireguard server
- Configures firewall rules for the redirector

The VPS serves as the public-facing endpoint that receives C2 traffic from implants while keeping the actual C2 server location masked.

### Terraform

Terraform handles the Infrastructure as Code (IaC) aspects of PrimusRedir, automating:

- VPS provisioning and configuration
- DNS record management via Cloudflare API
- VPS firewall setup
- Resource cleanup and teardown

This ensures consistent, reproducible deployments and easy cleanup when engagements are complete.

### Dual-Listener setup

Some C2 frameworks like Havoc require a dual-listener setup to properly handle the redirector architecture. This is because:

1. **External HTTPS Listener**: Generates implants that connect to the redirector over HTTPS (the public domain)
2. **Internal HTTP Listener**: Catches the decrypted traffic forwarded by Caddy from the redirector

PrimusRedir includes a built-in feature that outputs the Havoc listener configuration when the `--c2-variant havoc` flag is specified. It automatically generates the complete listener setup, including:

- **Listener 1**: External HTTPS listener for implant generation
- **Listener 2**: Internal HTTP listener for Caddy traffic forwarding



The listener config can then be copied into the havoc profile and then all the listeners will be running when you spin up havoc. 

## Conclusion 
As stated, I built this tool to have a simple way of spinning up redirectors for a wide range of C2 frameworks quickly, with room for customization and expandability. After being a private project for quite a while, it’s finally going public as an open-source release.

---

![mando](/assets/img/mando.gif)