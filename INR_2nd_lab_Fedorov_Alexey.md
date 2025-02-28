# INR Lab 2: IPv4 & IPv6

## Completed by Fedorov Alexey (tg: @ullibniss)

---

# Task 1 - Ports and Protocols

```
Using your network topology from the previous lab, you will be gathering some information
about your machines.
You might need to substitute the "dummy" Worker VPC node from Lab 1 topology by the
Ubuntu Cloud Guest instance. So, the lab topology could be as following:
```

I swapped the worker PC with Ubuntu Cloud Guest.

![image](https://github.com/user-attachments/assets/fa6ffa90-31f6-4f1e-b191-029f16095c9a)

I also added a second gateway for the router because the first one does not provide network access. And as I said in the previous lab, the interface with internet access has a router that does not allow connections between LAN hosts.

![image](https://github.com/user-attachments/assets/010cc06f-a8a4-4f1c-92cf-e9898fc2aebd)

![image](https://github.com/user-attachments/assets/316daab2-6da6-4512-9fb5-503fd4e5fb3e)


## 1.1 Check the open ports and listening Unix sockets against ssh (22) and http (80) on Admin and Web respectively.

### netstat

`netstat` (or its modern alternative ss) - is a network utility tool used to display network connections, routing tables and network protocol statistics. 

Let's check the web port with `netstat`. I will use the `ss` utility instead of `netstat` because `netstat` is deprecated.

![image](https://github.com/user-attachments/assets/0da6ef85-b632-4c86-9df7-c3d1cce10bd5)

We can see that `nginx` opened port 80 and is LISTENING for connections.

### lsof

`lsof` - is a powerful utility used to display information about files that are currently open by processes on a system. It is \*musthave\* tool for identifying network connections and open files related to network activity. It can be used to trace which processes are holding active sockets, whether they are listening for incoming connections or communicating over the network.

The next step is to check the Admin SSH port (22) with `lsof`:

![image](https://github.com/user-attachments/assets/aa7780b4-b913-475e-ac35-00fa33852031)

We can see that port 22 is open and LISTENING for connections.

## 1.2 Scan your gateway from the outside. What are the known open ports?

I can scan the host using `nmap` with the following command:

```
nmap 172.19.0.100
```

![image](https://github.com/user-attachments/assets/e02793bc-0fda-4018-b1d0-b1e59b1a10f6)

There are many ports open, and our ports 22 and 8080 are in this list.

## 1.3 A gateway has to be transparent, you should not see any port that is not specifically forwarded. Adjust your firewall rules to make this happen. Disable any unnecessary services and scan again.

We see there are several ports open, and some of them seem to be unknown to `nmap`. Let's check the running services on these ports:

![image](https://github.com/user-attachments/assets/ac9f5bfd-59f3-4351-be9f-2c48ec254c75)

There are open ports in the list; we can close them by disabling the services. If we use firewall rules, `nmap` will still see the ports, but their status will be `filtered`.

![image](https://github.com/user-attachments/assets/d3dc692a-d66c-42b4-b01f-4e11389ebabb)

Let's scan with `nmap` again.

![image](https://github.com/user-attachments/assets/c64a897f-c786-4553-9674-9fd79de36b4e)

Port 2000 is still open. To close it, we need to disable the bandwidth-server.

![image](https://github.com/user-attachments/assets/f7c4f090-1286-4664-95f6-93c3967ee7df)

![image](https://github.com/user-attachments/assets/1b25ea58-6383-45ad-b86b-f24387a53fd3)

Now we have only our ports open and port 23 (telnet port for shell connection).

## 1.4 It suppose that some scanners start by scanning the known ports and pinging a host to see if it is alive.

### 1.4.1 Scan the Worker VM from Admin. Can you see any ports?

I used `nmap` to scan `Worker` from `Admin`.

![image](https://github.com/user-attachments/assets/53e0d73a-c830-4ad6-8e13-3b6e545c8ebc)

The only port that is open is 22. This is because `sshd` is pre-installed and enabled on Ubuntu Cloud.

### 1.4.2 Block ICMP traffic on Worker and change the port for SSH to one that is above 10000

To block ICMP, I reconfigured the `/etc/sysctl.conf` option:

```
net.ipv4.icmp_echo_ignore_all=1
```

![image](https://github.com/user-attachments/assets/9b3e409a-f33e-450a-940d-4d67345880fd)

![image](https://github.com/user-attachments/assets/9d1b945d-9669-40fd-a91c-dcd5ab8c97b9)

I also changed the SSHd port in `/etc/ssh/sshd_config` with the option:

```
Port 13377
```

![image](https://github.com/user-attachments/assets/56683524-bd7a-4358-bb95-631610dec62d)

### 1.4.3 Scan it without extra arguments.

I executed the `nmap` scan again.

![image](https://github.com/user-attachments/assets/f14211be-3118-409a-bf35-2a678e8c743f)

Since the port is out of the range 1-1000, it was not detected. But the host is still UP, despite the ICMP block. This is because `nmap` operates using more than just the ICMP protocol to detect if a host is UP.

### 1.4.4 Now make necessary changes to the command to force the scan on all possible ports.

Let's modify the command to:

```
nmap -p 1-65534 172.10.0.100
```

![image](https://github.com/user-attachments/assets/c8843f36-10a6-4012-9083-fc5741dd0ae9)

Now the SSH port was detected.

### 1.4.5 Gather some information about your open ports on Web (ssh and http).

![image](https://github.com/user-attachments/assets/af40c664-b745-4fdd-bf34-e99c3560529d)

As we can see, ports 22 (SSH) and 80 (HTTP) are open.

# Task 2 - Traffic Captures

## 2.1 Access your Web's http page from outside and capture the traffic between the gateway and the bridged interface.

I will use Wireshark for this purpose. I will scan the `br-c371f19bc2ea` interface.

![image](https://github.com/user-attachments/assets/7f808938-3813-471b-800a-435845d17535)

### Can you see what is being sent?

Yes. I can see that an HTTP request was sent from `172.19.0.1` to `172.19.0.100` (packet 6) and back (packet 10). I can also see the TCP handshake (SYN -> SYN+ACK -> ACK) (packets 2, 3, 4).

### What kind of information can you get from this?

This is HTTP traffic, because of this, I can retrieve the entire web page.

![image](https://github.com/user-attachments/assets/07a88958-8878-40e6-880b-1d0026325c2c)

### What do the headers mean?

Headers I had are default HTTP headers:

- **`User-Agent`**: Identifies the client (browser/device) making the request.  
- **`Accept`**: Lists MIME types the client can handle.  
- **`Referer`**: Shows the URL of the page that linked to the request.  
- **`Accept-Encoding`**: Lists supported compression methods for the response.  
- **`Accept-Language`**: Specifies preferred languages for the response.  

## 2.2 SSH to the Admin from outside and capture the traffic (make sure to start capturing before connecting to the server).

I started capturing traffic and then connected to `Admin` with `SSH`.

![image](https://github.com/user-attachments/assets/1aee8e59-820a-426c-a1cf-c6fa565c770d)

### Can you see what is being sent?

Yes and no. I can see what kind of packet is being sent. It is SSHv2 and TCP. But I cannot see the packet content because it is encrypted. I can still see the TCP handshake (packets 11, 12, 13).

Encrypted data:

![image](https://github.com/user-attachments/assets/869e7538-a5d3-45b8-a816-e5cc0b43bc48)

### What kind of information can you get from this? What are the names of the ciphers used?

I see the exchange of supported SSH protocol versions: `SSH-2.0-OpenSSH_8.9p1` and `SSH-2.0-OpenSSH_9.6p1`. 

Moreover, there was a key exchange: the client and server reached a consensus on a key exchange protocol: `Elliptic Curve Diffie-Hellman Key Exchange Init`.

## 2.3 Configure the Burp suite as a proxy on your machine and intercept your HTTP traffic.

I installed BurpSuite and opened the proxy tab. Then I started the browser and turned on interception.

![image](https://github.com/user-attachments/assets/f834ec7c-c257-4942-b973-2f990ddec6ab)

### Show that you can modify the contents by changing something in the request.

Let's visit our `Web` page.

![image](https://github.com/user-attachments/assets/5ac9ca50-8ca6-40c2-8556-0c71a1acfdda)

As we can see, the request was intercepted. Now we can change any information that will be sent to the server.

We can also intercept responses.

![image](https://github.com/user-attachments/assets/aaafc819-8fb0-4c7c-9ece-f800a7c4e5b6)

Let's change the web page.

![image](https://github.com/user-attachments/assets/363df517-cdce-4a9a-b63e-f7c52d8ea68b)

![image](https://github.com/user-attachments/assets/b4ae0fd3-3316-491a-97e1-0a85635b356c)

### Why are you able to do this here and not in an SSH connection?

HTTP traffic is insecure, allowing any information in a request to be modified, unlike SSH, which uses public-key cryptography to protect against man-in-the-middle attacks.

# Task 3 - IPv6

## 3.1 Configure IPv6 from the Web Server to the Worker. This includes IPs on the servers and the default gateway.

First, I reconfigured all hosts and the router.

Router:

![image](https://github.com/user-attachments/assets/05f951f5-b21c-40fe-a660-ce3ca4fa9ddc)

Hosts:

![image](https://github.com/user-attachments/assets/119781e2-82e9-4db6-9f15-b5372c2d0dda)

![image](https://github.com/user-attachments/assets/717a7d63-839d-48e7-84a6-99e58e23323b)

![image](https://github.com/user-attachments/assets/8ce5d052-8a78-4f36-ba4b-c8a7bf6e77c4)

Let's test connection:

![image](https://github.com/user-attachments/assets/c732c1d5-39ec-4497-8496-9faa7176a2e8)

It works!

## 3.2 Access the Web's http page using IPv6 from Admin while capturing the traffic again. Can you see the difference? What's the difference in packages? Explain.

I started the capture. Let's access the page.

![image](https://github.com/user-attachments/assets/e729a96a-cad2-4fa1-a568-ff52b1360593)

![image](https://github.com/user-attachments/assets/d6324b6a-316b-43e1-93ff-ae4b8738a02a)

In this scenario, we observe that both the source and destination addresses are expressed in IPv6 format (16 bytes). Additionally, the ICMPv6 protocol is used for Neighbor Advertisement. Moreover, the length of the IPv6 packet is shorter compared to IPv4 due to the absence of fields that are present in IPv4 packets.

## 3.3 Practice in IPv6 addresses compressing and decompressing. Write your used IPv6 addresses both in full and compact mode. Provide the calculation chain.

Web:
- Short: 2001:db8:1::3
- Long: 2001:0db8:0001:0000:0000:0000:0000:0003

Admin:
- Short: 2001:db8:1::2
- Long: 2001:0db8:0001:0000:0000:0000:0000:0002

Worker:
- Short: 2001:db8:2::2
- Long: 2001:0db8:0002:0000:0000:0000:0000:0002

Router:
- Short: 2001:db8:1::1
- Long: 2001:0db8:0001:0000:0000:0000:0000:0001
- Short: 2001:db8:2::1
- Long: 2001:0db8:0002:0000:0000:0000:0000:0001

### Calculations

Long -> Short
1) Remove leading zeros from each hextet.
2) Replace consecutive zero hextets with `::`. Only one sequence of zero hextets is replaced by `::`.

Short -> Long
1) Determine the number of hextets (n), and calculate N = 8 - n.
2) Insert N zero hextets where `::` is located.
3) Restore leading zeros in the remaining hextets.

# Reference

- https://ru.wikipedia.org/wiki/IPv6-%D0%B0%D0%B4%D1%80%D0%B5%D1%81
- https://www.reddit.com/r/commandline/comments/15uaes9/netstat_vs_ss_socket_statistics/
- https://unix.stackexchange.com/questions/412446/how-to-disable-ping-response-icmp-echo-in-linux-all-the-time
- https://portswigger.net/burp/documentation/desktop/tools/proxy/intercept-messages
- https://help.mikrotik.com/docs/spaces/ROS/pages/328247/IP+Addressing
