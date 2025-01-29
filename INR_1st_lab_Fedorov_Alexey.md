# INR Lab 1. Networking Basics.

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

I verified that virtulization is enabled in my bios.

I also added my user to required groups:

![image](https://github.com/user-attachments/assets/18dfbea1-ca3a-4547-b93b-8483c3fdf171)

Finally, I installed `GNS3`

## 1.2 Start a GNS3 project, configure the pre-installed Ubuntu Cloud Guest template. Check that you can start it.

I created GNS3 project and downloaded `Ubuntu Cloud Guest` template. Then I imported it into `GNS3`.

![image](https://github.com/user-attachments/assets/ad8af84c-bf2e-48de-886a-96e509ac0429)

Added instance to project.

![image](https://github.com/user-attachments/assets/f63a18e8-c7da-4189-bedd-c6fc5cc89e52)

Press start to check whether it works.

![image](https://github.com/user-attachments/assets/3b942a61-063b-47f6-9972-864c59fb7036)

It works!

## 1.3. What are the different ways you can configure internet access in GNS3? Test them with a single created VM and give a one-line description of each. What are the differences between them? Bonus: show the difference between them on practice and test the connectivity.

### 1 - NAT node

NAT node is like real NAT provides IP masquarade that allows to cast local-public IPs. Moreover this node automatically runs DHCP server.

![image](https://github.com/user-attachments/assets/1e4b8958-70f3-4ad3-8e8b-aa918ac906a2)

![image](https://github.com/user-attachments/assets/6e008db2-92df-4cb9-9e28-a29c85d5e3c9)

### 2 - Cloud node (Bridge)

Cloud node is bridge adapter to host machine network interface. You connect virtual machine as it in your local network.

![image](https://github.com/user-attachments/assets/486ecdc2-c6b2-4103-8f01-d8da9713f127)

![image](https://github.com/user-attachments/assets/a7559173-4b1e-47b0-bd3f-a0f81e1bad2c)

# Task 2. Switching

## 2.1 Make the following network topology

![image](https://github.com/user-attachments/assets/4e61291c-69a6-42fc-a3ef-5e952251301d)

I did.

![image](https://github.com/user-attachments/assets/ac9948d3-dff7-4e58-901c-d75f411e26fd)

## 2.2 Install openssh-server on both VMs and nginx web server on the Web VM.

I installed only openssh-server on Admin.

![image](https://github.com/user-attachments/assets/2c49eda0-a702-4aa3-ae15-62ec8907ae6d)

And Installed openssh-server & nginx on Web.

![image](https://github.com/user-attachments/assets/c5343adc-57b0-450c-a829-77ac9afcb161)

## 2.3 What is the IP of the mask corresponding to /28 ? How many machines can you configure under this subnet? Explain it.

IP mask corresponding to /28 is 1111111.11111111.11111111.11110000 or 255.255.255.240.

This implies that 14 machines can be set up within the network. Why is that? Let's examine the bitmask. When a bit is set to 1, it's referred to as a network bit; otherwise, it's a host bit. The number of hosts is determined using the formula:

`hosts = 2^hostBits - 2` 

We subtract two hosts because the first is a network identifier, and the last address is reserved for the broadcast address.

## 2.4 Configure the VMs with private static IPs under a /28 subnet.

I will use netplan to reconfigure VM's. Default netplan configuration is:

![image](https://github.com/user-attachments/assets/7b14ec2a-4106-4473-9e35-aa2e524f097b)

I changed it to the following:

Web
![image](https://github.com/user-attachments/assets/4c378308-9673-4e31-8784-38a728fedaa9)
![image](https://github.com/user-attachments/assets/96e62f22-069d-4cb4-b252-b6df50313cda)

Admin
![image](https://github.com/user-attachments/assets/de90ebe7-a155-48e8-9979-f50d80169fd6)
![image](https://github.com/user-attachments/assets/9fa59e55-3e68-4147-bad9-1cdfde6378b8)

## 2.5. Check that you have connectivity between them.

Let's use ping. First is Admin -> Web.

![image](https://github.com/user-attachments/assets/159a6784-b0f9-415c-8176-7424901de5ad)

Web -> Admin.

![image](https://github.com/user-attachments/assets/ede8bcea-50d8-4134-9e36-d83b2cf013a5)

Connected!

6. Make sure your web server is accessible from the Admin VM.

I used curl to get the page.

![image](https://github.com/user-attachments/assets/f3d1e814-2145-477b-916f-d1f1c36fa6a9)

## Task 3. Routing

## 3.1 Select a virtual Routing solution (Gateway) such as Mikrotik (recommended default choice), PfSense, VyOS, Untangle NG, OpenWrt, Cumulus VX.

I installed `MicrotiK CHR` applicance.

![image](https://github.com/user-attachments/assets/45018a0d-3879-47e4-ad9c-f00af39d069a)

Next step,I built topology.

![image](https://github.com/user-attachments/assets/3186dbf3-d51b-436b-9e93-987195591fcc)

## 3.2 Create Internal network for Worker instance


