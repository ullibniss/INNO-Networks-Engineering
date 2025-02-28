# INR Lab 1: Networking Basics

## Done by Fedorov Alexey

---

# Task 1. Tools

```
To make it easier to prototype network topologies and troubleshoot them, you will be using GNS3
software to emulate networks.

1. Install the needed dependencies for GNS3: QEMU/KVM, Docker and Wireshark.
Hint: Check that virtualization is enabled in your bios. Make sure that the user belongs to all the
necessary groups after installing the tools.

2. Start a GNS3 project, configure the pre-installed Ubuntu Cloud Guest template. Check that
you can start it.

3. What are the different ways you can configure internet access in GNS3?
Test them with a single created VM and give a one-line description of each.
What are the differences between them?
Bonus: show the difference between them on practice and test the connectivity
```

## 1.1 Install the needed dependencies for GNS3: QEMU/KVM, Docker and Wireshark. Hint: Check that virtualization is enabled in your bios. Make sure that the user belongs to all the necessary groups after installing the tools.

`QEMU/KVM` is Installed.

![image](https://github.com/user-attachments/assets/45cc73fc-80f7-40ec-9474-f3f2cb42d228)

`Docker` is installed.

![image](https://github.com/user-attachments/assets/a3f5a628-d5e8-4e50-ab3a-7cf0e2463d13)

`Wireshark` is installed.

![image](https://github.com/user-attachments/assets/daff6d19-e756-4f68-8722-a08ef25cd063)

I verified that virtualization is enabled in my BIOS.

I also added my user to the required groups.

![image](https://github.com/user-attachments/assets/18dfbea1-ca3a-4547-b93b-8483c3fdf171)

Finally, I installed `GNS3`

## 1.2 Start a GNS3 project, configure the pre-installed Ubuntu Cloud Guest template. Check that you can start it.

I created a GNS3 project and downloaded the `Ubuntu Cloud Guest` template. Then, I imported it into GNS3.

![image](https://github.com/user-attachments/assets/ad8af84c-bf2e-48de-886a-96e509ac0429)

I added the instance to the project.

![image](https://github.com/user-attachments/assets/f63a18e8-c7da-4189-bedd-c6fc5cc89e52)

I pressed start to check whether it works.

![image](https://github.com/user-attachments/assets/3b942a61-063b-47f6-9972-864c59fb7036)

It works!

## 1.3. What are the different ways you can configure internet access in GNS3? Test them with a single created VM and give a one-line description of each. What are the differences between them? Bonus: show the difference between them on practice and test the connectivity.

### 1 - NAT node

The NAT node, like a real NAT, provides IP masquerade, allowing the translation of local to public IPs. Moreover, this node automatically runs a DHCP server.

![image](https://github.com/user-attachments/assets/1e4b8958-70f3-4ad3-8e8b-aa918ac906a2)

![image](https://github.com/user-attachments/assets/6e008db2-92df-4cb9-9e28-a29c85d5e3c9)

### 2 - Cloud node (Bridge)

The cloud node is a bridge adapter to the host machine's network interface. It allows you to connect the virtual machine as if it were part of your local network.

![image](https://github.com/user-attachments/assets/486ecdc2-c6b2-4103-8f01-d8da9713f127)

![image](https://github.com/user-attachments/assets/a7559173-4b1e-47b0-bd3f-a0f81e1bad2c)

# Task 2. Switching

## 2.1 Make the following network topology

![image](https://github.com/user-attachments/assets/4e61291c-69a6-42fc-a3ef-5e952251301d)

I did.

![image](https://github.com/user-attachments/assets/ac9948d3-dff7-4e58-901c-d75f411e26fd)

## 2.2 Install openssh-server on both VMs and nginx web server on the Web VM.

I installed only the `openssh-server` on Admin.

![image](https://github.com/user-attachments/assets/2c49eda0-a702-4aa3-ae15-62ec8907ae6d)

And I installed `openssh-server` and `nginx` on Web.

![image](https://github.com/user-attachments/assets/c5343adc-57b0-450c-a829-77ac9afcb161)

## 2.3 What is the IP of the mask corresponding to /28 ? How many machines can you configure under this subnet? Explain it.

The IP mask corresponding to `/28` is `11111111.11111111.11111111.11110000` or `255.255.255.240`. 

This implies that 14 machines can be set up within the network. Here's why: 

Let's examine the bitmask. In a `/28` subnet, 28 bits are reserved for the network portion, leaving 4 bits for hosts. The formula to calculate the number of available host addresses is:

`hosts = 2^hostBits - 2`

Substituting the number of host bits (4):

`hosts = 2^4 - 2 = 16 - 2 = 14`

We subtract 2 because the first address is reserved as the network identifier, and the last address is reserved for the broadcast address. Thus, 14 host addresses are available for devices in this subnet.

## 2.4 Configure the VMs with private static IPs under a /28 subnet.

I will use `netplan` to reconfigure the VMs. The default `netplan` configuration is:

![image](https://github.com/user-attachments/assets/7b14ec2a-4106-4473-9e35-aa2e524f097b)

I changed it to the following:

Web

![image](https://github.com/user-attachments/assets/4c378308-9673-4e31-8784-38a728fedaa9)

![image](https://github.com/user-attachments/assets/96e62f22-069d-4cb4-b252-b6df50313cda)

Admin

![image](https://github.com/user-attachments/assets/de90ebe7-a155-48e8-9979-f50d80169fd6)

![image](https://github.com/user-attachments/assets/9fa59e55-3e68-4147-bad9-1cdfde6378b8)

## 2.5. Check that you have connectivity between them.

Let's use `ping`. First, Admin -> Web.

![image](https://github.com/user-attachments/assets/159a6784-b0f9-415c-8176-7424901de5ad)

Web -> Admin.

![image](https://github.com/user-attachments/assets/ede8bcea-50d8-4134-9e36-d83b2cf013a5)

Connected!

6. Make sure your web server is accessible from the Admin VM.

I used `curl` to get the page.

![image](https://github.com/user-attachments/assets/f3d1e814-2145-477b-916f-d1f1c36fa6a9)

## Task 3. Routing

## 3.1 Select a virtual Routing solution (Gateway) such as Mikrotik (recommended default choice), PfSense, VyOS, Untangle NG, OpenWrt, Cumulus VX.

I installed the `MikroTik CHR` appliance.

![image](https://github.com/user-attachments/assets/45018a0d-3879-47e4-ad9c-f00af39d069a)

## 3.2 Create Internal network for Worker instance

The next step was to build the topology.

![image](https://github.com/user-attachments/assets/3186dbf3-d51b-436b-9e93-987195591fcc)


## 3.3 Connect your Gateway to the internet and to your workstation/host

I connected it to the internet through the Cloud. To get an IP address, I had to configure `dhcp-client` on the router. Let's check connectivity.

![image](https://github.com/user-attachments/assets/bd779145-8aca-44a1-bed9-70ce3cf4994d)

Unfortunately, the Innopolis router does not allow connections between hosts in the LAN. Because of this, I connected the router to a local bridged network and configured the IP manually with the address `172.19.0.100`.

![image](https://github.com/user-attachments/assets/e15fe541-abde-4bc3-9418-a1810044aab2)

## 3.4 Setup the gateway for Admin, Web and Worker, then check their connectivity. 

I created two networks: Internal (Worker) and External (Web & Admin).  

- **Internal**: `172.10.0.0/24`  
- **External**: `192.133.7.0/24`  

Let's configure the router and hosts.

Router configuration:

![image](https://github.com/user-attachments/assets/5a5a2207-e208-4665-8ed7-4bec7d3afbdb)

Web and Admin configuration:

![image](https://github.com/user-attachments/assets/c6b9cb78-7b3c-4a48-a5f2-5f0e53f41949)

![image](https://github.com/user-attachments/assets/7bb9e4c0-574d-44b1-b4f7-31cc16b6caff)

Worker:

![image](https://github.com/user-attachments/assets/068f53f2-35b9-4439-b3b0-0836ece80182)

Let's check connectivity from Web:

![image](https://github.com/user-attachments/assets/5bc76283-8528-4c4c-b7c4-5bec9787428e)

Everything works!

## 3.5 Configure port forwarding for http and ssh to Web and Admin respectively. 

To forward ports, I have to configure firewall settings. I will add two rules for each host:

Forward rules:

![image](https://github.com/user-attachments/assets/c68851c0-b42d-4226-a3f4-c1c0ab6c3282)

Here are the firewall rule options:

- **dst-address**: The destination address to forward from.  
- **dst-port**: The destination port to forward from.  
- **to-address**: The address to forward to.  
- **to-port**: The port to forward to.  
- **chain**: The firewall chain to which the rule will be added (`forward`, `input`, `output`).  
- **action**: The type of action to take (accept`, `drop`, `redirect`, `masquerade`).  

## 3.6 Check that you can ssh to the Admin and access your web page from your workstation/host.

Let's check the web page. I made a request from the host machine:

![image](https://github.com/user-attachments/assets/3324b4bf-fedb-4ef0-a12c-50cf6d899075)

SSH to admin:

![image](https://github.com/user-attachments/assets/8e1b7ef5-36e4-472f-b98c-94c4e3d781c0)

Everything works!

# References

- https://docs.gns3.com/docs/
- https://netplan.readthedocs.io/en/stable/examples/
- https://www.youtube.com/watch?v=BQRIFZwil-s
- https://help.mikrotik.com/docs/spaces/ROS/pages/3211299/NAT
