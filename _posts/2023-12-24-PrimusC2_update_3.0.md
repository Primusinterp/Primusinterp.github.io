---
title: PrimusC2 - New Release | Update 3.0
categories: [PrimusC2, updates]
tags: [AD, Red Team, C2, PrimusC2, ExecuteAssembly]    
---

![Primusinterp's Github logo](/assets/img/Primusc2.png)
--- 
I am very happy to have this new update of PrimusC2 released. I have completed a lot of the tasks on the roadmap and made more Qol stuff that makes the C2 more nice to use and work with!

[PrimusC2 Github link](https://github.com/Primusinterp/PrimusC2)

## What's new:
- HTTP based implant 😎 
    + Execute .NET Assembly in memory
    + Registry persistance capability 
    + Download of files from target
    + Callback interval(Sleep) adjustable
    + Server a static webpage if browsing to '/' of the C2 server
    + STABILLITY
- RC4 encryption of data streams on HTTP implant
- Revamp of *pwsh_cradle* functionality
- Quality of life stuff
    + Autocomplete commands improvements
    + Payload directory listing
    + Network interface autocomplete 
    + Add payloads from Payload folder to autocomeplete automatically 
    + Payload server now serving the files from the *Payloads* folder
    + Include AMSI status on callback
    + Updated command names 
- Updated requirements.txt

Im going to elaborate on some of the changes in the coming sections. 

### HTTP Implant 
For a long time, it's been on my to-do list to develop an HTTP-based implant. Now it's finally become a reality, and it brings a lot of new cool features and improved stability for the implant compared to the TCP-based implant. I have focused on making the code more readable and modular, so it's easier to implement features and get a sense of what's going on in the code. In the coming section, I will show some of the new functionality.

Here is a picture showing of of the new HTTP implant in action 😎 
![execute_asm](/assets/img/HTTP_callback.png)

The generation of a listener is very simple and smooth with the new automatic and dynamic autocomplete system.

#### RC4 Encryption
As mentioned above, I have implemented RC4 encryption for all the data streams within the HTTP implant. RC4 is by no means a "secure" encryption algorithm at all, but it brings some much-needed OPSEC to PrimusC2. There is still a lot of work to be done regarding the OPSEC of the framework, but this is a great step in the right direction.

Below is just a quick snippet of how the traffic looks within a packet capture:
![encryption](/assets/img/encryption.png)

#### Stability of execute-ASM
Execute-ASM works as intended within the TCP implant; however, it can be very unstable and cause a lot of bugs and trouble when executing. The implementation in the HTTP implant is way more stable, and I have not seen any bugs while testing it myself. This is huge, as the operartor does not need to fear for his life while running Rubeus 🤣

Here SharpWMI is executed, firstly with the "-h" command:
![WMI](/assets/img/sharpwmi.png)

Afterwards, the command to enumerate which antivirus is running is executed without any issues!
![WMIres](/assets/img/sharpwmiresult.png)

#### Download
Another item from the roadmap that I have implemented is the download functionality. It brings the ability for the operator to exfiltrate data with PrimusC2, which is pretty cool!

![download](/assets/img/Download.png)

"Stuff" exfiltrated to the loot directory: 

![download](/assets/img/loot.png)

### Quality of life and command handling
My goal is to make the framework "nice" to use, so I will keep on trying to implement small quality of life stuff whenever i can :) I've made some improvements to the autocomplete system, and I have implemented several ways to make it more dynamic and make it easier for the operator to "autocomplete" stuff.

When generating a listener, PrimusC2 will now look through the available network interfaces on the system and then make them available for autocompletion. The same concept has been applied to the payloads present in the payloads folder. When the server is executed, it will look through the payloads folder and add payloads to the autocomplete list.

![interfaces](/assets/img/interfaces.png)

Another cool feature added is the *payloads* command, which will do a directory listing of the payloads folder and add the contents to the autocomplete list if any new payloads have been added.

![payloads](/assets/img/payloads.png)

### Conclusion
I hit some pretty big milestones with this update and got the HTTP implant working along with a bunch of cool features. A lot of much-needed stability is also introduced with this update (the HTTP implant). I focused on making the new implant more structured and easier to modify in order to make it easier to implement new features in the future. A lot of bugs still exist and a lot of features could use an overhaul, so i will continue to work on this when i have the time!


This is it for now. I hope that you will enjoy the update, and I would love to hear your feedback or answer any questions that you might have.

Thanks for reading, and have an excellent day.


### Roadmap
- [x] Execute-Assembly 
- [ ] Inline-Assembly
- [x] Encryption of data streams
- [ ] Implementation of smart pipe redirectors with automation
- [x] Download functionality for the implant
- [ ] Upload functionality for the implant
- [x] Directory operations
- [x] HTTP C2 channel 
- [ ] Improve OPSEC
- [ ] Evasion techniques
- [ ] Custom Term Rewriting Macro


![mando](/assets/img/mando.gif)
  