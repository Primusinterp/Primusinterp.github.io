---
title: PrimusC2 - New Release | Update 4.0
categories: [PrimusC2, updates]
tags: [AD, Red Team, C2, PrimusC2, ExecuteAssembly, Caddy, Redirector, HTTPS]    
toc: true
---

![Primusinterp's Github logo](/assets/img/Primusc2.png)
--- 
Time for another update for PrimusC2. This update do not have as much content as the previous updates, but it has some stuff that greatly improves the maturity of the project. 

[PrimusC2 Github link](https://github.com/Primusinterp/PrimusC2)

## What's new:
- HTTPS based implant 😎 
- Smart Pipe Redirector setup
    + Implant callback via domains
    + cloudflare DNS
    + Automatic SSL cert via Caddy 
    + Caddy 
    + Modularity
- Updated help menu
    + Focus on improving the 'ease of use'
    + Structure to the help menu
    + Examples included for most commands
- Introduction of a Web Interface
    + Listener Generation 
    + Callbacks/Interact
    + Generate implants
    + Payload list with upload functionality 
- Fixed callback time not updating with correct time
- More documentation [here](https://primusinterp.com/PrimusC2/)
- Optimization of backend stuff
- Bug fixes

Im going to elaborate on some of the changes in the coming sections. 

### HTTPS Implant 
I reworked the structure of the HTTP implants to use a new and more flexible HTTP system. This also enabled me to create an implant that supports HTTPS which improves the OPSEC of project quite a bit. The HTTPS implant can only be used in tandem with the newly introduced Smart Pipe Redirector that supports using domains as the callback address. The Implant itself has the same capabilities as the HTTP implant introduced in update 3.0. 


### Smart Pipe Redirector setup
The Smart Pipe Redirector is the "Heavy hitter" in this update. It provides the framework with an automated method of provisioning a redirector that uses HTTPS, and greatly reduces the workload for the operator once the initial setup has been completed. The redirector consists of the following parts:

- Cloudflare for DNS.
- Caddy as the reverse proxy and traffic filtering.
- Wireguard connection between the VPS and the C2 server.
- Digital Ocean VPS
- Terraform as IaC

The redirector needs an initial setup, however after the setup, you will have a fully working redirector provisioned in about 3 minutes. When you exit out of PrimusC2 the redirector will automatically be torn down and all resources will be destroyed. See the [docs](https://primusinterp.com/PrimusC2/) for information about the setup process.

Caddy is the reverse proxy used on this redirector, it takes care of creating an automatic SSL certificate, traffic filtering and redirection of traffic. Caddy is pretty awesome and i plan to make a separate blog post about the redirector setup in the near future.  At the moment Caddy has some basic block lists implemented for User-Agents and IP-Addresses. This is easily configured within the main Caddyfile which is the configuration file that Caddy uses. 

Below is just a quick snippet of an active HTTPS VPS redirector:
![Redirector](/assets/img/Redirector_provisioned.png)

And if you visit the site, a benign message is being shown(easily changed) and you can see that it has a valid SSL certificate. 
![site-visit](/assets/img/caddy_block.png)

### Updated help menu
The framework lacked a proper help menu(Thanks [Lsecqt](https://www.youtube.com/watch?v=-yzP6Fwt9jo&t=3s)😎) that helped new operators understand the functionality and the intended uses of different commands. So i went ahead and created a help menu for all the commands available at this time. 

An example of the new help menu in action:
![help menu](/assets/img/Primus-help.gif)

If you have feedback or suggestions, please reach out!


### Webinterface
Another big addition is the introduction of a web interface. The interface makes the framework more simple to use and provides a better overview. I am by no means skilled in website development, so it's not the most stable, cool or best-practice interface. I tried to go for a simplistic look with a all the basic features available in this first iteration. In the future i would love to add more functionality and control to the web interface. The current iteration consists of the following parts:

- Home 
- Listener 
- Callbacks
- Payloads

A short walkthrough of the new interface can be seen below:
![walkthrough](/assets/img/Primus-web-walktrough.gif)

### Credit 
A colleague of mine []() is on his Python learning journey. I tasked him with writing me a simple Python script, that would auto-generate me some Star Wars related names. He created a cool function that i could use in the `pwsh_cradle` command 😎

### Conclusion
This update introduces some pretty significant additions to the framework and certainly helps with improving the maturity of the project. With the new redirector setup and the HTTPS implant a whole new lane of opportunity has opened up and im excited to built upon it in the future. The introduction of the help menu and the web interface helps with improving the accessibility of the framework and hopefully makes it more pleasant to use. A lot of small changes have been made to the backend as well. However one of my next big goals is to rewrite the backend so it uses a proper database instead of everything living in confusing Lists, Dicts strings etc 😭.


This is it for now. I hope that you will enjoy the update, and I would love to hear your feedback or answer any questions that you might have.

Thanks for reading, and have an excellent day.


### Roadmap
- [x] Execute-Assembly 
- [x] Encryption of data streams
- [x] Implementation of smart pipe redirectors with automation
- [x] Download functionality for the implant
- [ ] Upload functionality for the implant
- [x] Directory operations
- [x] HTTP C2 channel 
- [ ] Improve OPSEC
- [ ] Rework backend to accomodate a database for persistant storage
- [ ] Evasion techniques
- [ ] Custom Term Rewriting Macro


![mando](/assets/img/mando.gif)
  