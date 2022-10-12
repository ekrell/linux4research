# iCORE Workshop: Linux for Researchers

Many researchers are using Linux for a variety of reasons, either on their workstation or a remote server. 
Student researchers, graduate and undergrad, often find themselves suddenly working in Linux without any training. 
The purpose of this tutorial is to address some common questions and concerns and provide suggestions on best (or at least better) practices. 
This is followed by a brief introduction to scripting, using examples based on common research tasks. 

This tutorial is not about command line basics. There are an incredible amount of tutorials and books on using commands likes `head`, `ls`, `cat`, etc. I see no need for additional redundancy. Even after learning how to work with the commands, many basic tasks are still unclear to many new Linux users. For example, many think that they must have admin priviledges to install any software or will share files with other users by opening up all permissions despite security risks. Another classic: setting an alarm to go off every 15 minutes so they can interact with a remote session to avoid getting logged out and their process killed. These are among the issues addressed here.

See also: [Evan Krell's tutorial for using High Performance Computing resources for processing DNA data for bioinformatics, etc.](https://github.com/ekrell/hpc-dna-tutorial) It is from 2016, but I expect will still be applicable for the most part. 

**innovation in COmputing REsearch (iCORE):**

- website: http://icore.tamucc.edu/
- twitter: https://twitter.com/ICORE_TAMUCC
- youtube: https://www.youtube.com/channel/UCvsK07PvushTI2BA2BhN-DQ
- google calendar: https://calendar.google.com/calendar/u/0?cid=Y2JlNDZodnIwZXV0NmZzN2h1bWs2NnB2dnNAZ3JvdXAuY2FsZW5kYXIuZ29vZ2xlLmNvbQ


## Clients & Servers

A very common source of confusion for Linux beginners is the client-server relationship. 

- Server: a computer that you connect to remotely. May host data and services for a large number of users (but not always). 
- Client: the machine you are working from that uses the server's resources
- Very common for researchers to run their code on a remote server that has more powerful resources than their personal machine
- Secure Shell (SSH): a network protocol for logging into a remote computer from a local computer
    - [`ssh`](https://www.ssh.com/academy/ssh/command): a software implementation of the SSH protocol. Also called an SSH client. Use it to log into remote computers. Similar to PuTTY on Windows.
    - [`scp`](https://www.ssh.com/academy/ssh/scp): Copy a file from one machine to another

Consider the following: 


    ekrell@krell:~$ ls
    anaconda3    cuda-testing    Downloads    Music    opencv    Programs    R    Videos
    Desktop    Public Tools    Documents    examples.desktop    Pictures    Templates    Zotero
    ekrell@krell:~$ ssh ekrell@riddler.tamucc.edu
    You are accessing a Texas A&M University - Corpus Christi ("TAMU-CC") information system.

    • Unauthorized use is prohibited;
    • Usage may be subject to security testing and monitoring;
    • Misuse is subject to criminal prosecution; and
    • Users have no expectation of privacy except as otherwise provided by applicable privacy laws; and
    • By logging in, you are agreeing to the rules and conditions set forth by Texas A&M University-Corpus Christi here: IT Acceptable Use Policy

    ekrell@riddler.tamucc.edu's password: 
    Last login: Tue Oct 11 20:35:31 2022 from 99-111-138-226.lightspeed.crchtx.sbcglobal.net
    [ekrell@riddler ~]$ ls
    cosc2465  ekrellLab3  hw4  kraytracer.zip  LINKEDLISTS.cpp  newdir  playground  temp  TSA-V_severity.tsv
    2437labs  changed.txt  cosc3336_DatabaseFinalScript.sql  ekrell.sql  IMGPROC  KRELL_IMGPROC.tar.gz  LINKEDLISTS.h
    [ekrell@riddler ~]$ exit
    logout

    Connection to riddler.tamucc.edu closed.
    ekrell@krell:~$ 

There is actually a lot going on. A simplied explanation: 

1. I was at my desktop computer in a terminal emulator (command line program in a GUI window manager)
2. There exists a computer with the IP address `23.221.222.250`
3. Instead of having to memorize that number, the Domain Name Service (DNS) is used to map human-friendly names to IP addresses
4. The TAMUCC IT have a tradition of name the CS student's homework server after Batman villains. Currently, it is `riddler.tamucc.edu`. We can call it Riddler. 
5. Following the SSH protocol, Riddler is running a program (`sshd`) that accepts incoming remote logins
6. Following the SSH protocol, I used the `ssh` program to connect to Riddler using its domain name
7. I am asked for my password, which is the password for my account on Riddler
8. Since the SSH protocol is based on cryptography, anyone monitoring my network traffic will see an encrypted version of the traffic, including the password
9. Having logged in, the commands I enter are being performed on Riddler even though I am sitting at my home desktop



## Transfering files

Clone this repo

    git clone git@github.com:ekrell/linux4research.git
    cd linux4research


Mirror websites

Hosting simple html sites

  (Auto-make directory into site)
  




    
    

   

### Managing remote sessions

- A common task is to run long processes on a remote server. 
- If you simply run a command from a remote terminal session, that process is tied to that session. 
- When you log out, the process is killed. 
- But most systems have a time-out where you will be logged out if inactive for a (typically short) while. 
- Putting your laptop to sleep, etc will also close the session. 
- Here, we will:
  - Briefly familiarize with Linux processes
  - Send jobs to the background and then back to the foreground
  - Create and manage remote sessions that are not tied to your terminal sessions using `screen` and `tmux`

#### Manage processes

**Concepts**

- Program: sequence of instructions (passive)
- Process: program in execution (active); Or, an instance of a program
- Programs -> processes is a 1:M (one-to-many) relationship
- Processes are maintained within the Kernel's process table
- Thread: processes are composed of at least one thread of execution -- First thread in process is called task group leader -- Other threads in process are linked through a list of nodes
- Task: an executable entity; a general term for process or thread

**Process monitoring**

- [`ps`](https://man7.org/linux/man-pages/man1/ps.1.html): shows information about active processes on the system
- [`pgrep`](): 
- [`top`]():
- [`htop`](): 


    ps -aux
    
    pgrep
    
    top
    
    htop

Stop a running process

    kill <pid>
    
    kill -<signal> <pid>
    
    pkill 
   
   
Send process to background with `&` and `!`  

    wget -q "ftp://ftp.ncbi.nlm.nih.gov/blast/db/nt.??.tar.gz" &!
    sh &!
    
Close terminal, then log back in
    
See background jobs

    jobs
    
Bring a job back to the foreground

    fg 1
    
    
But what if I am already running it? Pause the process with `Ctrl-z`, then send to background

    wget -q "ftp://ftp.ncbi.nlm.nih.gov/blast/db/nt.??.tar.gz" & 
    ^Z
    
    bg
    disown
    
#### Using `screen` 

The previous approach allows you to shuttle processes back and forth between background and foreground, but has drawbacks:

- A somewhat tedious process is required for each process
- Process must be disowned at the time of exiting this system. What if you time or forget and close your laptop, etc?

An alternative is using `screen` or `tmux` to create sessions that you interact with, but that are independent of your terminal's remote session.
I prefer `tmux`, but `screen` is often already installed on Linux systems. 


#### Using `tmux` 

### Install software without root

- Researchers are likely to work at least partially on Linux servers without admin (or _root_) priviledges. 
- Software is typically installed on request
- But can be slow
- Or, you might have a number of small executables that don't make sense to be made available for all users
- Why do you need root to install programs? Because users should not modify system-wide files
- But do they **need** to be system-wide? Depends... 

#### One way to install: write it yourself

- Every time you write a C program and compile it, or even a python/R/shell script, etc....
- You just added an executable program to the computer, installing it for yourself in a directory you own
- This doesn't "feel" like installing, but how is it not?
- Maybe we would feel like it installed if we can access it from anywhere,like the other system commands

        # Make a new executable program
        nano program.sh
                echo "This is a script, a type of non-compiled program"
        
        # Set executable permission
        chmod +x program.sh
        
        # Run it
        ./program.sh
        
        # Go to prevous directory and try to run it
        cd ..
        ./program.sh
        linux4research/program.sh
        
        # Create local executables directory
        cd ~
        mkdir bin
        
        # Move your script
        cp linux4research/program.sh bin/program
        
        # Modify your `$PATH` variable so that your environment will check that directory for programs
        nano ~/.bashrc 
            export PATH=$PATH:bin  # Add this line to the end
        bash  # Update environment
        
        # Now run it from anywhere
        program
        
#### Install programs from the web to your local programs directory

**Install `tree`**


**Install `tmux`**





### Collaboration within Linux


### Managing SSH keys

### Package management

Software is typically installed and maintained with a package manager. Software is bundled into a package that includes the executable file, manual pages, config files, and whatever else is needed. Since the major distributions have their own package manager (or package system), you have some degree of comfort that the packages will mesh well with the system. In other words, if you are installing software in Ubuntu with `apt-get`, the packages should conform to typical Ubuntu conventions. Typical Linux software exist both as source code available for manual installation and as pre-built binaries in packages in package repositories. 

Two major package systems: 

| Package system | Major distributions | 
|----------------------|--------------------------| 
| Debian style (.deb) | Debian, Ubuntu, Raspbian | | Red Hat style (.rpm) | Red Hat, Fedora, CentOS |


**Package System Overview**

- Package files:
    - compressed collection of files that make up the software
    - Scripts for how to install it
    - Metadata: description, etc
    - Package maintainers do the job of turning source code into packages
- Repositories
    - Where packages are stored
    - The major distros maintain repos of thousands of packages
    - Centralized repos often have software versions far behind the latest; partially for security & reliability
    - Distros often have multiple repos:
        - Stable: the main repo where packages have been well-tested and seem reliable
        - Testing: for those who want to look for bugs before release
        - Non-free: proprietary software, especially device drivers
    - But various repos exist. The original software developers may have their own repo for faster releases
- Dependencies
    - Package systems require some means of dependency resolution
    - Linux software makes heavy use of shared, dynamically-linked libraries
    - Package managers can install all needed dependencies

| Package system | Low-level tools | High-level tools |
| Debian style | dpkg | apt, apt-get, aptitude |
| Red Hat style rpm 	yum, dnf

Quick note about updates: Debian has `apt-get update` and Red Hat has `dnf check-update`. Neither installs anything. In the case of Debian, it updates the package index file, which apt uses for finding packages, which new versions are available, etc. You need to do this before `apt-get upgrade` so that apt is actually looking at the latest state of the packages. Some idea with Red Hat.

Package management tasks

- Find a package in a repo
    - Debian: `apt-cache search <searchstring>`
    - Red hat: `dnf search <searchstring>`
- Install a package from a repo
    - The typical way to install packages
    - Debian: `apt-get install <package_name>`
    - Red hat: `dnf install <package_name>`
- Install a package from package file
    - There will be not dependency resolution! Will just tell you there is an issue and halt.
    - Useful if you want to research a vulnerable package long since removed from the repo
    - Debian: `dpkg -i <package_file.dpkg>`
    - Red hat: `rpm -i <package_file.rpm>`
- Remove packages
    - Very handy since Linux installs pieces of packages across FHS, not a single "programs" directory
    - Debian: `apt-get remove <package_name>`
    - Red hat: `dnf remove <package_name>`
- Update packages
    - As noted before, be sure to use apt-get update or dnf check-update.
    - Debian: `apt-get upgrade`
    - Red hat: `dnf upgrade`
- List installed packages
    - Debian: `dpkg -l`
    - Red hat: `rpm -qa`
- Check if a particular package is installed
    - Debian: `dpkg -s <package_name>`
    - Red hat: `rpm -q <package_name>`
- Get information about a package
    - Debian: `apt-cache show <package_name>`
    - Red hat: `dnf info <package_name>`
- List dependencies
    - Useful to make sure you don't install a package you know you don't want
    - Debian: `apt-cache depends <package_name>`
    - Red hat: `dnf repoquery --requires --resolve <package_name>`
- Determine which package installed a particular file
    - Debian: `dpkg -S <file_name>`
    - Red hat: `rpm -qf <file_name>`
- Determine which package provides file
    - Debian: `dpkg-query --search '/etc/passwd'` <-- Full path for both Debian and Red Hat
    - Red hat: `dnf provides */iscsiadm` <-- But supports substition to indicate that we don't care about the full path
- View enabled repositories
    - Debian: `apt-cache policy`
    - Red hat: `dnf repolist`
- Add new repository
    - Debian: `sudo add-apt-repository ppa:libreoffice/ppa`
    - Red hat: `dnf config-manager --add-repo="https://mirror.aarnet.edu.au/pub/centos/7"`
- Remove repository
    - Debian: `sudo add-apt-repository -r ppa:<repo to remove>`
        - Look into `ppa-purge` for how to also downgrade/remove all packages installed with that repo
    - Red hat: `rpm -e rpmfusion-free-release-28-1`
    - You might want to just disable a repo. Look into the best practices for disable vs remove.
- View the installation history
    - Debian: `tail -n 25 /var/log/apt/history.log` <- for checking the last 25 entries. Could use less, nano, etc.
    - Red hat: `dnf history`

### Virtual environments & containers



### Setting up a remote Jupyterlab server

### Managing config files




