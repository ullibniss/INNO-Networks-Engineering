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


Let's scan with `nmap` again.


Port 2000 still opened. To close it we need to disable bandwidth-server.

