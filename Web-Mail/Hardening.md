# First 15 - Hardening

## Information

*Get information on the server for documentation:*

	`hostname`  
	`cat /etc/*-release`  
	`uname -a`  
	`ifconfig`  
	`netstat -rn`  

## Update
    `sudo dnf upgrade --refresh -y`

## Check Users

Check for login Users:  
	`less /etc/passwd | grep bash`

Check list of all Users:  
	`compgen -u`

Check for sudo users:  
	`getent group sudo`

Checking for sudoers user:  
	`sudo visudo`

Adding user to sudoers file:  
	`sudo visudo`
		(replace username with with account name)
		`john ALL=(ALL:ALL) ALL`

Remove User from Group :  
	`gpasswd -d {user} {group}`

Change Root Password:  
	`passwd {user}`  

Lock un-used users:  
	`passwd -l {user}`  

Disable user account  
	`sudo usermod -L -e 1 {user}`  

## Check Services

List Services:  
	`sudo systemctl --status-all`

Stop a services:  
	`sudo systemctl stop {servicename}`

Disable a services:  
	`sudo systemctl disable {servicename}`  

Check running processes:  
	`ps`  
	`ps -ef`  
	`ps -aux`  

Check listening ports:  
	`sudo netstat -tulpn | grep LISTEN`  
	`sudo netstat -antp`  

### Common Ports  
*Note ports that are used by services that the system should have running*  

*SMTP - Encrypted - TLS/STARTTLS  - 465*  
*SMTP - Unenrypted - 25*  
*IMAP - Encrypted - TLS/SSL - 993*  
*IMAP - Unencrypted - 143*  
*POP3 - Encrypted - TLS/SSL - 995*  
*POP3 - Unencrypted - 110*  
*DNS - 53*  

## Cron

*List scheduled cron jobs for current user:*  
`crontab -l`  

*List scheduled cron jobs for root user:*  
`sudo less /etc/crontab`  

*List scheduled cron jobs for specific user:*  
`sudo crontab -u {username} -l`  

*Edit/delete cron jobs*  
`sudo nano /etc/crontab`  


## Secure cron

 To prevent users except for root from creating cron jobs, perform the following steps.

1) Create an empty file /etc/cron.allow:
	`sudo touch /etc/cron.allow`

2) Allow users to create cron jobs by adding their usernames to the file:
	`sudo echo "{username}" >> /etc/cron.allow`

3) To verify, try creating a cron job as non-root user listed in cron.allow. You should see the message:
	`crontab -e`
	`no crontab for "{username}" - using an empty one`

4) Quit the crontab editor and try the same with a user not listed in the file (or before adding them in step 2 of this procedure):
	`crontab -e`
	`You ({username}) are not allowed to use this program (crontab)`
	`See crontab(1) for more information`


## Secure at

To prevent users except for root from scheduling jobs with at, perform the following steps.  

1) Create an empty file /etc/at.allow:  
	`sudo touch /etc/at.allow`

2) Allow users to schedule jobs with at by adding their usernames to the file:
	`sudo echo "{username}" >> /etc/at.allow`

3) To verify, try scheduling a job as non-root user listed in at.allow:  
	`at 00:00`
	`at>`  

4) Quit the atprompt with Ctrl–C and try the same with a user not listed in the file (or before adding them in step 2 of this procedure):  
	`at 00:00`
	`You do not have permission to use at.`




## SSH

### Restarting the SSHD service  
*After making and changes restart sshd service:*  
`sudo systemctl restart sshd`

#### Putting SSH on non-standard port

1) Edit ssh config file  
	`sudo vim /etc/ssh/sshd_config`

2) In that file look for the line:  
 	`#Port 22`

3) Uncomment and Change that line to the desired port:  
 	`Port 13350`
	
4) Save and close

5) Restart ssh
   
#### If using SELinux: 

1) Check to see if SELinux is Enforcing  
	`sestatus`

2) List ports  
 `Sudo semanage port -l | grep ssh`	

3) Add New Port to SELinux Policy  
	`sudo semanage port -a -t ssh_port_t -p tcp [desired port]`


#### Configuring Firewall rules for SSH change

1) Opening New Port for SSH:  
   `sudo firewall-cmd --add-port=33000/tcp --permanent`
 
 2) Update Firewall rule  
    `sudo firewall-cmd --reload`

3) Disabling the standard SSH port  
	`sudo firewall-cmd --remove-service=ssh --permanent`	

4) Reload firewall  
   `sudo firewall-cmd --reload`

5) Restart SSH daemon  
   `sudo systemctl restart sshd`

#### Disabling root login  
Open sshd config file  
`sudo vim /etc/ssh/sshd_config`

locate the line: `PermitRootLogin`  
change to: `PermitRootLogin no`

##### Disallow anonymous access 
add line `AllowUsers !nobody`
