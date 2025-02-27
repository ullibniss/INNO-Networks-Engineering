# Lab 2

## Done by Fedorov Alexey

---

# Task 1 - Ports and Protocols

```
Using your network topology from the previous lab, you will be gathering some information
about your machines.
You might need to substitute the "dummy" Worker VPC node from Lab 1 topology by the
Ubuntu Cloud Guest instance. So, the lab topology could be as following:
```

I swapped worker PC with Ubuntu Cloud:

![image](https://github.com/user-attachments/assets/fa6ffa90-31f6-4f1e-b191-029f16095c9a)

I also added second gateway for router, because first one does not provide network access. And as I said in previous lab, interface with internet access has router that does not allow connection beetwen LAN hosts.

![image](https://github.com/user-attachments/assets/010cc06f-a8a4-4f1c-92cf-e9898fc2aebd)

![image](https://github.com/user-attachments/assets/316daab2-6da6-4512-9fb5-503fd4e5fb3e)


## 1.1 Check the open ports and listening Unix sockets against ssh (22) and http (80) on Admin and Web respectively.

Let's check Web port with `netstat`. I will use `ss` utility instead of netstat, because `netstat` is deprecated:

![image](https://github.com/user-attachments/assets/0da6ef85-b632-4c86-9df7-c3d1cce10bd5)

We can see that `nginx` opened port 80 and LISTENING for connections.

Next step is to check Admin SSH port (22) with lsof:

![image](https://github.com/user-attachments/assets/aa7780b4-b913-475e-ac35-00fa33852031)

We can see that port 22 is opened and LISTENING for connections.

## 1.2 Scan your gateway from the outside. What are the known open ports?

I can scan host using `nmap` with the following command:

```
nmap 172.19.0.100
```

![image](https://github.com/user-attachments/assets/e02793bc-0fda-4018-b1d0-b1e59b1a10f6)

There are many ports opened and our 22,8080 ports are in this list.

## 1.3 A gateway has to be transparent, you should not see any port that is not specifically forwarded. Adjust your firewall rules to make this happen. Disable any unnecessary services and scan again.

We see there are several ports opened and some of them are seemed to be unknown for the nmap . Let's check the running services on these ports:

![image](https://github.com/user-attachments/assets/ac9f5bfd-59f3-4351-be9f-2c48ec254c75)

There are opened ports in list, we can close them by disabling services. If we will use firewall rules, `nmap` still will see ports, but status will be `filtered`

![image](https://github.com/user-attachments/assets/d3dc692a-d66c-42b4-b01f-4e11389ebabb)

Let's scan with `nmap` again.

![image](https://github.com/user-attachments/assets/c64a897f-c786-4553-9674-9fd79de36b4e)

Port 2000 still opened. To close it we need to disable bandwidth-server.

![image](https://github.com/user-attachments/assets/f7c4f090-1286-4664-95f6-93c3967ee7df)

![image](https://github.com/user-attachments/assets/1b25ea58-6383-45ad-b86b-f24387a53fd3)

Now we have only our ports opened and 23 (telnet port for shell connection).

## 1.4 It suppose that some scanners start by scanning the known ports and pinging a host to see if it is alive.

### 1.4.1 Scan the Worker VM from Admin. Can you see any ports?

I used `nmap` to scan `Worker` from `Admin`.

![image](https://github.com/user-attachments/assets/53e0d73a-c830-4ad6-8e13-3b6e545c8ebc)

The only port that opened is 22. This is because sshd pre-installed and enabled on Ubuntu Cloud.

### 1.4.2 Block ICMP traffic on Worker and change the port for SSH to one that is above 10000

To block ICMP, i reconfigured `/etc/sysctl.conf` option:

```
net.ipv4.icmp_echo_ignore_all=1
```

![image](https://github.com/user-attachments/assets/9b3e409a-f33e-450a-940d-4d67345880fd)

![image](https://github.com/user-attachments/assets/9d1b945d-9669-40fd-a91c-dcd5ab8c97b9)

I also changed SSHd port in `/etc/ssh/sshd_config` with option:

```
Port 13377
```

![image](https://github.com/user-attachments/assets/56683524-bd7a-4358-bb95-631610dec62d)

### 1.4.3 Scan it without extra arguments.

I executed `nmap` scan again.

![image](https://github.com/user-attachments/assets/f14211be-3118-409a-bf35-2a678e8c743f)

Since port out of range 1-1000, it was not detected. But host still is UP, despite the ICMP block. This is because `nmap` operates not only ICMP protocol to detect host is UP.

### 1.4.4 Now make necessary changes to the command to force the scan on all possible ports.

Let's modify command to:

```
nmap -p 1-65534 172.10.0.100
```
![image](https://github.com/user-attachments/assets/c8843f36-10a6-4012-9083-fc5741dd0ae9)

Now ssh port was detected.

### 1.4.5 Gather some information about your open ports on Web (ssh and http).

![image](https://github.com/user-attachments/assets/af40c664-b745-4fdd-bf34-e99c3560529d)

As we can see, 22(ssh) and 80(http) are opened.

# Task 2 - Traffic Captures

## 2.1 . Access your Web's http page from outside and capture the traffic between the gateway and the bridged interface.

