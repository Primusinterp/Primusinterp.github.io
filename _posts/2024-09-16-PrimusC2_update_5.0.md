---
title: PrimusC2 - New Release | Update 5.0
categories: [PrimusC2, updates]
tags: [AD, Red Team, C2, PrimusC2, ExecuteAssembly, Caddy, Redirector, HTTPS, Steal token, ]    
toc: true
---

![Primusinterp's Github logo](/assets/img/Primusc2.png)
--- 
Time for another update for PrimusC2. It includes significant changes to the project in order to create a more stable baseline for upcoming updates. 

[PrimusC2 Github link](https://github.com/Primusinterp/PrimusC2)

## What's new:
- Migrated to using a DB 😎
    + Persistent sessions 
    + All logic use the DB for data retrieval and storage
    + Almost got rid of the use of global vars and non-persistent data types 
- TCP implant disabled
    + Needs a complete rework
- EXE, Shellcode and DLL compilation 
    + Choose what format you want the payload in
- Added Docker compatibility for easier use 
    + No need for any dependency hell 
- Web GUI updated
    + Add notes to callbacks
    + Fetch data from the DB
    + Output Searcher
    + Added more compile options
    + Shows callback data 
- Implant updates
    + Returns more detailed windows version
    + Steal_token
    + rev2self
    + whoami
    + tShell
    + Usercontext
- CLI visual updates with "arrow menus"
    + Better visibility
    + Shows current user context when interacting with a callback 
- More documentation [here](https://primusinterp.com/PrimusC2/)
- Optimization of backend stuff
- Bug fixes
    + Sleep
    + pwsh_cradle

Im going to elaborate on some of the changes in the coming sections. 

### DB migration 
In order to create a more stable baseline and make it significantly easier to work with data in the backend and frontend, it was necessary to move away from using different data types, such as lists, dicts, tuples etc. and migrate everything over to using a DB instead. I went with SQLite, as this was my first time working with a DB and it turned out to be quite easy and straight forward to work with. With a DB implemented it was also possible to create persistent sessions. This means that listeners can be restarted and old authorization keys can be reused, so any existing implants can talk with the server. With this change you do not have to start over if a fatal exception occurs, instead just restart the server you are back in action. 

With the DB implemented, it also enables me to start the improvement of the stability of the framework and handle more exceptions (which is still on my todo).


### TCP implant disabled
I have decided to disable the TCP implant in this update (the code is still, it is just disabled). My reasoning behind this change is that the TCP version of the implant is quite outdated and clumsy compared to the rest of the implants. I would like to perform a complete rework of it before re-enabling it again. 

### EXE, Shellcode and DLL compilation 
To expand upon the flexibility of PrimusC2, i have added the option for multiple payload formats that can be generated upon request. This will give the operator the ability to freely choose how to deploy the implant. The DLL is created with the exported function: Ost. The shellcode is created using the [sRDI](https://github.com/monoxgas/sRDI) project, that converts a DLL to PIC shellcode.

![compile_options](/assets/img/compile_options.png)


### Docker compatibility
I decided to port the whole project over to a docker setup as well. This is done to make the C2 more usable and portable. You do not have to fight dependencies and weird errors when using this setup. It should work out of the box (famous last words...)

If you find the docker setup to be broken in any way, please let me know. 

### Web GUI updated
The web GUI has gotten some additional new features and fixes. The biggest thing is of course that the data is now persistent and stored in the database. But i added some small quality of life stuff, that i want to include here as well. First up is the ability to add notes to each callback, in order to help track callbacks and their data.

![note_cb](/assets/img/Note_CB.gif)

Additionally, a new feature has been implemented, which i call the "Output Searcher". It can be used to search trough all the callbacks for a specific command or output that is buried in one of the callbacks. This feature is especially useful when many callbacks have been received and the operator can't remember in which callback that some specific data is located.

![output_searcher](/assets/img/output_searcher.gif)

Lastly i also added the callback data in the left corner of the callback screen (see the GIF above), to avoid unessecarry clicking in and out of the interact screen. To accommodate the new compile options, the option to choose between the different ones were added as well.

### Implant updates
This update also contains some new additions to the implants. One of the cooler features added is the `steal_token` & `rev2self` commands. This will allow the operator to steal and impersonate access tokens from an arbitrary processes (as long as the implant is High integrity). As of now, the implant is not entirely "impersonation aware" (i need to do some research in modifying the existing functionality to be completely impersonation aware). However, in order for the `steal_token` functionality not to be useless, i added a new command called `tShell`. It will use the stolen token to execute commands via cmd.exe (i know it's not perfect, but it's a start). Both the GUI and CLI have been updated to reflect what the current user context is. Lastly, a simple `whoami` command was added as well. 

### Conclusion
This update is not as "sexy" as some of the previous ones, but it has achieved a massive milestone in terms of migrating all data handling to using a DB instead of non-persistent data types. This has laid out the building blocks and helped strengthen the foundation of the project in order to make it durable and easy to work with. Additionally, i did add some other small(but cool) things that mature the project just a tiny bit more. I have plenty of stuff on my internal to-do for the project, so stay tuned for that!


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
- [x] Rework backend to accommodate a database for persistent storage
- [ ] Evasion techniques
- [ ] Custom Term Rewriting Macro
- [ ] Refactor entire code base into multiple files and maybe classes 


![mando](/assets/img/mando.gif)
  