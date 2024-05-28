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
That's correct, and server respond us with supported functions. HTTP PUT is allowd on the target
