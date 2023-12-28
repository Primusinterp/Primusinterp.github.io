---
title: PrimusC2 - New Release | Update 2.0
categories: [PrimusC2, updates]
tags: [AD, Red Team, C2, PrimusC2, ExecuteAssembly]    
---

![Primusinterp's Github logo](/assets/img/Primusc2.png)
--- 
I am very happy to have this new update of PrimusC2 released. I have hit some pretty big milestones with the project and done some cool stuff.

[PrimusC2 Github link](https://github.com/Primusinterp/PrimusC2)

## What's new:
- File Operations
    + cd
    + ls 
    + pwd
- Execute .NET Assembly in memory (HYPE)
- Revamp of data transfer between server and implant 
- Added more exception handling (not bulletproof yet)
- Changed how the implant handles commands (no more of accidentally firing of a command)
- Quality of life stuff
    + Command History 
    + Autocomplete Commands 
- Removed persistence functionality (will implement another version in the future) 

Im going to elaborate on some of the changes in the coming sections. 

### Execute .NET Assembly 
This one is huge for me, i wanted from the start to include some more advanced functionality in the C2, to elevate it from being an *Advanced Reverse Shell Handler*. I have now successfully implemented the execution of .NET assemblies in memory. 

Here is a short demo of the new functionality in action 😎 
![execute_asm](/assets/img/execute_asmv2.gif)

The functionality for execute assembly has been tested with a couple of popular offensive .NET tools, such as *Rubeus*, *SharpWMI*, *Seatbelt* and so on. The current state of the functionality is still quite unpredictable and can do weird stuff sometimes (still working on that). Large output from tools, seems to break it, and I haven't found a proper solution for that yet. However, I am still very pleased with the progress I have made so far.


### File Operations
PrimusC2 now has some proper directory operations on the machine; it does not rely on PowerShell anymore to do: *ls*, *cd* and *pwd*, this makes it easier to traverse the filesystem and see what goodies that are hidden on the desktop 👀

Here you can see it action:
![mando](/assets/img/FileOps.gif)

### Quality of life and command handling
Ive made some small (but nice) quality of life improvements. Ive added command history (Arrow Up/Down) to make it more dynamic, and I have added autocompletion for the native commands in PrimusC2. Furthermore, I have changed the way commands are handled on the implant side. Preivoulsy, when a command was executed from the server side, the implant would try and execute that as a powershell command directly. Now the implant only has unmanaged powershell and can execute commands with Windows CMD as well, but you need to specify either *pwsh* or *shell* :) 

### Conclusion
My plan is to keep developing on this when I have the time to make more cool features, but also more stable and less prone to crap out if something goes wrong or the operator mistypes anything. It has turned out that using TCP as a C2 channel can be quite challenging, and sometimes it lives its own life. In the future, I am going to explore HTTP as a C2 channel to perhaps bring more stability to the C2. 

Big shoutout to all the other nim projects out there, they helped me great deal while working on this update!

This is it for now. I hope that you will enjoy the update, and I would love to hear your feedback or answer any questions that you might have.

Thanks for reading, and have an excellent day.


![mando](/assets/img/mando.gif)
  