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

## Part 1: Common researcher tasks

### Transfering files

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

Monitor processes

    w

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

Compile your own code

Install `tmux` 

Create a scripts directory, add to path


Share your scripts with others


### Managing SSH keys

### Package management

### Virtual environments & containers

### Setting up a remote Jupyterlab server


### Collaboration within Linux




