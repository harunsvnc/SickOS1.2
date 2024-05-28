I will add here my methods to gain root level privileges on SickOS1.2.
After importing machine to my virtualization platform, I run arp-scan --localnet command to find out the ip address of the machine.
It seems our target's ip address is 192.168.146.174.
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/4feb2abc-6121-4505-b471-69eab86fd073)
Hence I run a nmap scan to discover open ports and running services.
![image](https://github.com/harunsvnc/SickOS1.2/assets/75423540/20678f86-2d61-4a4a-bcb9-053d1b54407e)
 Port 22 and 80 is running on the target. Hence i run a nikto scan to get further data on the web application.
 
