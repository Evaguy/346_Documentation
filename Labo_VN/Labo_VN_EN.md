We're going to create, and configure, a virtual network (VN) in Azure, then we'll add two virtual machines to see if they're communicating well between each other

## Goals :
- Create a VN in Azure.
- Add two VM and test the communication.

## First step : Create and configure the virtual network
Firstly, we're going to create a virtual network, then configure its settings, by going into the research bar of Azure and researching "Virtual Network". 

![VN search](img/vn_search.png)

Then, click on "Create".

![VN creation](img/vn_create.png)

You can also see, in the page, all the virtual network created in the same Azure subscription as yours.

### Base informations
[//]: <> (Check the multiple names, they're basic translation of what was written in Azure.)
After you've pressed the button "Create", you'll arrived in the first section of the creation of a virtual network, called : "Base informations".
Here, it is important to precise, in "Project details", your subscription (which normally add itself automatically) and your ressource group, so that Azure doesn't create a new group (in my case, my ressource groupe is called : "pr38ehf").

The name of the virtual netowrk, in "Instance details", can be anything you like (in my case, I've chosen : "VN-GabinChautemps"), and like your subscription, the region will add itself automatically.

![Instance details](img/vn_details.png)

[//]: <> (Reworte the first half omg :/)
After "filling the gaps", click on "Next".

### Security
In the security section, it is possible to configure multiple security functionality, like a firewall, directly into the virtual newtowrk. However, for simplicity, we won't do it here. So click, again, on "Next".

### IP addresses
Now, we'll configure the range of the private IP addresses in our virtual netowrk.

A private IP address is an address which is used in a private network (ex. entreprise network, home network) given by the DHCP protocole (Dynamic Host Configuration Protocol). It allows devices connected to the same network to communicate with each other safely and easily. However, it's not possible to "surf" on the Internet with a private IP address.

There's three class of private IP address :
- Class A :
	- From 10.0.0.0 to 10.255.255.255
- Class B :
	- From 172.16.0.0 to 172.31.255.255
- Class C :
	- From 192.168.0.0 to 192.168.255.255

For this documentation, I'll use the class C for addressing, but the handling is the same with another class.

For starters, set your private IP address range to CIDR 23 (/23). CIDR allows you to create “super networks” with greater flexibility than other addressing methods.

![Setting private IP range](img/ip_config.png)

Attention, check that your virtual network range doesn't overlap with another range, otherwise there will be some errors.

Add a subnet by clicking on "Add a subnet". This will open a new page.

After that, choose the name of your subnet (in my case, it's : Chautemps-Front).

![Setting subnet name](img/subnet_name.png)

The goal of the subnet can stay to "Default".

Then, choose the range of the subnet in CIDR 24 (so /24).

![Setting subnet range](img/subnet_range.png)

If you have a network security group (NSG), you can add it, in the security section (ex. mine's called : "SG-Gabin").

![Adding NSG](img/nsg_added.png)

The advantage of adding your network security group on your subnet is that you can filter your virtual network traffic using the rules of your network security group (just like with a firewall). This adds another layer of security. In this way, by defining precise rules, you protect your virtual machines from external attacks and prevent unauthorized access.

After adding your network security group (or not), click on "Add" down the page. You'll be able to see, in the section where you've configure the private IP addresses, your subnet.

![Showing subnet range](img/show_range.png)

Finally, click on "Verifiy + create", then on "Create".

### Using another DNS for your virtual network
This step is totally not requiered, but I'll still explain it in this documentation.

It's possible to configure a DNS, other than using the ones from Azure directly, in the settings of your virtual network. To do this, find your virutal network in the section "Virtual network". Then search in the research box, on the left of the page, "DNS Servers", and choose "Customized".

There's multiple free DNS servers, in my case, I've chossen the ones from Cloudflare (1.1.1.1 and 1.0.0.1)

![Setting DNS server](img/dns.png)

Click on "Save" to save the modifications.

## Second step : Adding virtual machines in the virtual network
To add virtual machines in your virtual network, you'll need to use its network interface :

![Network interface of a virtual machine](img/ext_network.png)

These options will appeared automatically when you create a new virtual machine. However, it is not possible to add already created virtual machines inside your virtual network.

[//]: <> (Honestly, this is kinda dumb ^.)
[//]: <> (Like, there's a way to do it without re-creating VMs.)
[//]: <> (But you have to do some funky shit before you finally get it.)

## Third step : SSH connection to the virtual machines
For the SSH connection, here's how I'll do it :

![SSH connection diagram to my machines](img/schéma_projet_réseau_virtuel(1).jpg)

Firstly, I'll start two instances of Linux using WSL (Ubuntu to connect on my first machine, and Kali Linux to connect to my second machine)

![WSL machines](img/wsl_machines.png)

Next, I've did the same steps below, on both machine, to connect on the virtual machines in Azure :
- Move the private SSH key in the folder "~/.ssh/".
- Change its permission to "READ ONLY".
- Connection using SSH, with the public IP adresse of the virtual machine.

[//]: <> (Add link to other doc here)
(i) For more informations about these steps, read this documentation.

![SSH connection to the virtuals machine](img/steps_to_ssh.png)
The difference between a public IP address and a private IP address is that the public ones are used for interacting on the Internet, they cannot be used in a private network, compared to the private ones. And here's their ranges :
- From 1.0.0.0 to 9.255.255.255
- From 11.0.0.0 to 126.255.255.255
- From 129.0.0.0 to 169.253.255.255
- From 169.255.0.0 to 172.15.255.255
- From 172.32.0.0 to 191.0.1.255
- From 192.0.3.0 to 192.88.98.255
- From 192.88.100.0 to 192.167.255.255
- From 192.169.0.0 to 198.17.255.255
- And finally, from 198.20.0.0 to 223.255.255.255

## Fourth steps : Communication tests
To check if our machines can communicate well, in our virtual network, we can "ping".

[//]: <> (Little something to add in the french documentation about ping)
"Ping" is the name of a command used to test the communication of a host (in our case, one virtual machine to another) on an network. It's available for every OS that have a networking capability.

Firstly. find the private IP address of the two virtual machine, with another useful command in your terminal :

```
ip a
```

[//]: <> (Like the ping paragraph, but for ip)
The ip command is a Linux networking tool for configuring/showing your network interfaces. Compared to ifconfig (command that's part of the older net-tools package), it provides additional functionalities and a more consistent syntax.

For exemple, here I've used the IP command with the option "a", so that it can show me every single info, including the private IP address of the virtual machines (what is highlighted in the screenshot down below).

[//]: <> (Check here if I need to blurred something here, I don't want people doxing me :p)
[//]: <> (Don't think there's a lot of infos, but just to be sure.)
!["ip a" done in both terminals](img/command_ip_a.png)

We can see that my first machine (the one that's on top) has the IP address "192.168.2.4/24", and the second machine (the one on that's on the bottom) has the IP address "192.168.2.5/24". We can also see that's in the private IP range I've configure in the beginning of the documentation.

So now that we have the private IP address of our virtual machines, we can finally ping them each other. So the first machine will ping the second machine and the second machine will ping the first machine. In my case the ping command for the first machine is :

```
ping 192.168.2.5
```

And for the second machine, the ping command is :

```
ping 192.168.2.4
```

!["ping" done in both terminals](img/pung.png)

You'll normally see every packets arrived to their destinations, with the time that it took. If that's the case, it means that the virtual machine can communicate well in the virtual network !

To stop the pings, you can use "CTRL+C" in your terminal.
