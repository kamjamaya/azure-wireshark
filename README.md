# Utilizing Wireshark to Explore Networ Traffic

Go to portal.azure.com to get started and sign up for a subscription. They give you the first 30 days free and after that it adapts to a pay as you go model.

After your account is set up we will create our first resource group.

Resource Group Configuration to Hold our Virtual Machines

From your home screen on the Azure portal, select the “Resource Group” icon. 



Next, click on the “create” blue icon in the middle of the screen to start the process of making a resource group. 


Here you will name your Resource group and determine the region for it.


Click “Review + Create” when finished.

You should see the screen like the one below to ensure that you created a valid Resource Group.



Lastly, click “Create” in the left bottom corner to officially create the Resource Group.




Configuring Virtual Machines
From your home screen on the Azure portal, select the “Virtual Machines” icon.


Next, click on the “create” blue icon in the middle of the screen or the top left corner and select the first option “Azure Virtual Machine.”

After that, we can start configuring our settings.


For the Resource group, select the one that we created initially. You can also name your Virtual Machine as well. Select the same region as your resource group. From here you can configure the settings however you like. In this example I decided to use a Windows 10 Pro image. 

Scroll down and you will see more configuration settings. Here are the ones that I selected: 
Select (Standard_E2s_v3 - 2 vcpus, 16 GiB memory ($97.09/month)
Create a username and password
Allow public inbound ports
Select inbound port RDP 3389
You have free will to configure however you like and to what best fits your needs. 



Also, check the licensing box at the bottom left corner.


After that, select “Review + Create” to have your Virtual Machine validated and then created. 

Congrats you just created your first Virtual Machine!

We will also create an additional Virtual Machine and have them interact with each other. 

Go back to the home page and click on Virtual Machines or type it in the search bar.

Next, click on the “create” blue icon in the top left corner and select the first option “Azure Virtual Machine.”

After that, we can start configuring our settings again.
For the Resource group, select the one that we created initially. You can also name your Virtual Machine whatever you like. Select the same region as your resource group. From here you can configure the settings. In this example I decided to use Ubuntu Server 22.04 LTS -x64 Gen2.

Scroll down and you will see more configuration settings. Here are the ones that I selected: 
Select (Standard_E2s_v3 - 2 vcpus, 16 GiB memory ($97.09/month)
Create a username and password
Allow public inbound ports
Select inbound port RDP 3389

Select the settings that best meet your needs or you can select the ones I chose for my virtual machine. 







Click Next twice to go to the Networking section of your virtual machine that you are currently configuring. Ensure that the Virtual Network that your first Virtual Machine is on is the same Virtual Network that your second Virtual Machine will be on as well. 


After verifying that, you can click on Review + Create at the bottom to have you second virtual machine officially created. 

How to Access the Virtual Machine Remotely
Now that we have created our Virtual Machines we can start observing traffic on them by using the network protocol analyzer, Wireshark. 

First, copy the public IP address of the Windows virtual machine. 


Now, in Windows you will go to the search bar on the taskbar and either look up MSTSC, which stands for Microsoft Terminal Services, or type in Remote Desktop Connection.



You should see something like what is pictured above, click open.

Past the copied IP address in this box.

 

Enter the username and password that you configured the virtual machine with in Azure for the credentials.






You should see a screen similar to this where you have a Windows 10 OS Virtual Machine showing on top of your computer’s OS. We are officially in the Windows Virtual Machine! You know you are in the Virtual Machine if the IP Address is showing at the top in the window. 

Utilizing Wireshark to Explore Network Traffic


Inside of the Virtual Machine click on Microsoft Edge.
Type in www.wireshark.org
Once installed, open Wireshark and filter for ICMP Traffic only. ICMP is a network layer protocol that relays messages concerning network connection issues. The "ping" command uses this protocol and is used to test connectivity between hosts. When we filter Wireshark to only capture ICMP packets and ping the private IP address of our Linux machine, we can visually see the packets on Wireshark.



We can inspect each individual packet and see the actual data that is being sent in each ping, as demonstrated in the picture below.



Next, we will perpetually ping the Linux machine with the command 'ping -t'. This will continually ping the machine until we decide to stop it. While the Windows machine is pinging the Linux machine we will go to the Linux machine and block inbound ICMP traffic on it's firewall. 



Once we do that we will stop receiving echo replies from the Linux machine. We will block ICMP by creating a new Network Security Group on the Linux machine that will be set to block ICMP. We can then re-allow the traffic by allowing ICMP in the NSG.





Observe SSH Traffic in Wireshark
 
On the Windows VM reset the Wireshark activity by clearing out the filters. Then in Powershell, use the command ssh, the username for linux that you created @ the private IP address of the linux VM. For example I entered “ssh labuser@10.0.0.5”


Powershell will ask “Are you sure you want to connect?” type yes.

It then prompts you to enter your password that you configured the Linux VM with. When you enter the password, notice that it will not appear on the screen for privacy reasons. SSH is a secure communication protocol, as it encrypts traffic. SSH is used when remotely connecting from one computer to another and issuing a command line. Now the prompt changes to a more personalized version now that you have entered your credentials. 
















For example, mine looks like this: 



This signals that we are officially in the Linux machine. Any prompts and keystrokes you type in while in the Linux machine will be observed over the network and encrypted. (enter screenshot) to show SSH traffic. 



Observe DHCP Traffic in Wireshark
DHCP uses UDP port 67 and 68 to assign IP addresses to client machines. 
Now we will use Wireshark to filter for the Dynamic Host Configuration Protocol (DHCP).We will request a new IP address with the command "ipconfig /renew". You can see this traffic pictured below.

Observe DNS Traffic in Wireshark
Now, we'll analyze DNS (Domain Name Server) traffic by filtering it on Wireshark. We will initiate DNS traffic by typing in the command "nslookup disney.com." This command essentially asks our DNS server what is Disney's IP address. DNS traffic utilizes port number 53. You can see this traffic pictured below.








Observe RDP Traffic in Wireshark
Remote Desktop Protocol is used when remotely connecting from one computer to another to obtain a remote desktop GUI. This operates on TCP port 3389. Below is an example of the RDP traffic in Wireshark.

