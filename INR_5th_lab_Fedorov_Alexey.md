# INR Lab 5: QoS

## Done by Fedorov Alexey

---

# Task 1 - Prepare your network topology

## 1.1 In the GNS3 project, select and install a virtual routing solution that you would like to use: Mikrotik (recommended), Pfsense, vyos and so on.

I used Mikrotik appliance.

## 1.2 Prepare a simple network consisting of at least one router and two hosts. About four hosts in the network are most optimal. You also might need Internet access for the hosts. It may be something like this, just for the imagination:

I prepared network topology. Here is configuration.

Router configuration:

![image](https://github.com/user-attachments/assets/21226b1e-1477-4827-98c0-f15df48f253d)

Clients configuration:

![image](https://github.com/user-attachments/assets/cd6ceb7b-4dfb-4ebc-bf5b-6276e4831714)

![image](https://github.com/user-attachments/assets/7843341c-0963-420f-b578-1274a42b5ed3)

![image](https://github.com/user-attachments/assets/30ef8bd8-4805-467e-8db6-3886fd29cf2c)

![image](https://github.com/user-attachments/assets/667aa17d-cd1d-4c36-bc89-e1c31b5e1760)

As a result I got the following topology:

![image](https://github.com/user-attachments/assets/68d02924-f168-4b91-b4a9-311451c80793)

# Task 2 - QoS learning & configuring

## 2.1 Let's start with a little theory. Briefly answer the questions or give one-line description what is it: Сlass of Service (CoS), ToS (Type Of Service), Differentiated Services Code Point (DSCP), Serialization, Packet Marking, Tail Drop, Head Drop, The Leaky bucket algorithm, The Token Bucket Algorithm, Traffic shaping, Traffic policing?


**Class of Service (CoS):** A networking approach that prioritizes traffic by categorizing it into different classes, commonly applied in Layer 2 (Ethernet) frames.  

**Type of Service (ToS):** An older mechanism within IP headers that designates packet priority and handling instructions for routing and traffic management.  

**Differentiated Services Code Point (DSCP):** A field in the IP header used to define traffic priority and handling, enabling Quality of Service (QoS) in contemporary networks.  

**Serialization:** The conversion of data into a sequential format or stream for transmission, often referring to how packets are transmitted over a network link.  

**Packet Marking:** The process of labeling packets with specific identifiers (such as DSCP or CoS values) to dictate how they should be treated within the network.  

**Tail Drop:** A queue management method where packets are discarded when the queue reaches capacity, typically affecting the most recently arriving packets.  

**Head Drop:** A queue management technique where packets are removed from the front of the queue, usually impacting the earliest arriving packets.  

**The Leaky Bucket Algorithm:** A traffic shaping mechanism that regulates traffic flow by controlling the rate at which packets exit the "bucket," preventing sudden bursts and ensuring smooth transmission.  

**The Token Bucket Algorithm:** A traffic shaping and policing method that permits packet transmission as long as tokens are available, allowing bursts up to a specified limit.  

**Traffic Shaping:** A technique for managing traffic flow by delaying or buffering packets to achieve consistent and predictable transmission rates.  

**Traffic Policing:** A method for monitoring and controlling traffic by enforcing strict rate limits, dropping or remarking packets that exceed the defined threshold.  

## 2.2 Configure your network as you decided above. After your network is configured (don't forget to show the main configuration steps in the report), try to set a speed limitation (traffic shaping) between the two hosts.

```
Hint: It was found that the virtual solution for the Mikrotik router has a "sewn" in the firmware
speed limit of not more than one megabit per second, that is, in these conditions, we can only
configure the speed limit of not more than one megabit per second. Try to verify this and to get
around the restriction? Otherwise, set the traffic limit to no more than one megabit per second.
```

Queues are used to control bandwidth and prioritize traffic in Mikrotic routers. For configuration, I can use either the `/queue/tree` or `/queue/simple` management tools. Typically, `/queue/tree` is employed for more detailed traffic control, while `/queue/simple` is used for quick and straightforward bandwidth restrictions.

To set a speed limit between `client-1` and `client-4`, I used `/queue/simple`. I configured an upload speed limit of 322K and a download speed limit of 322K.

![image](https://github.com/user-attachments/assets/64582e72-708b-40f0-8bac-ddc91db495e8)

## 2.3 Run a bandwidth testing tool, see what is the max speed you can get and verify your speed limitation. Compare the speed between the different hosts.

I used `iperf3` for testing. `iperf3` is a tool to check network performance. It measures speed, delay, and other network details between two devices. It works in client-server mode and is often used to find and fix network problems.

There server and client mode on `iperf3`. I will use client mode on client from i want to make request and server mode on client to i want make request.

### Test: client-1 -> client-4

Running client mode on `client-1` and server mode on `client-4`:

![image](https://github.com/user-attachments/assets/f475d96f-7d0e-407a-94b0-4bf87e8feffa)

![image](https://github.com/user-attachments/assets/c0e3b722-2cf2-49b0-b47e-312b2d89f745)

As we can see network speed is about 322Kbit/sec. Limitations works!

### Test: client-1 -> client-3

This test must show us that limitations work only for `client-1` -> `client-4` route.

Running client mode on `client-1` and server mode on `client-3`:

![image](https://github.com/user-attachments/assets/f9a98d47-d711-4246-b712-fbdd8dd730e4)

![image](https://github.com/user-attachments/assets/2cee85ea-76fc-40b5-8175-cfc8cb8742c0)

Bitrate - 1Mbits/sec. This means that limitation does not work for `client-1` -> `client-3` route. Everything correct.

## 2.4 While your bandwidth test is still running, try to download a file from one host to the other host and see what is the max speed you can get. If you have more than two hosts on the network, play around with different speed values and show it.

I need to create large file for testing. I used `dd` to generate file:

```
dd if=/dev/urandom of=test bs=322K count=1337
```

![image](https://github.com/user-attachments/assets/ecb4e497-91e5-4eea-b17d-9d05c3aa880b)

### Test: file transfer `client-1` -> `client-4`:

I can measure file transfer bitrate with `iperf3`.

![image](https://github.com/user-attachments/assets/6b0bb33d-ac8a-4440-bd82-48e4b1871ead)

![image](https://github.com/user-attachments/assets/28d57f0c-fb51-4d70-8e69-56383d480e59)

As we can see, file transfer has the same bitrate.

### Test: file transfer `client-1` -> `client-4`, but another limit

I changed limit for route. Limit is 133K.

![image](https://github.com/user-attachments/assets/3d36ddb5-33d9-4689-b609-af430f3014bc)

Let's run file transfer.

![image](https://github.com/user-attachments/assets/5502ec6d-628d-4b66-88bc-a8555a870ee2)

![image](https://github.com/user-attachments/assets/51b6d056-3b48-4cca-9516-49aa719bee34)

New limitations were applied.

## 2.5 Deploy and verify your QoS rules to prioritize the downloading of a file (or any other scenario) over the bandwidth test.

To configure QoS rules, I user the mangle table in firewall. Mangle is a firewall functionality that allows marking and modifying packets to enable advanced routing, traffic shaping, and bandwidth management. 

I set up two rules: 

- For transfer traffic
- For iperf3 test traffic

Both rules are port-based. Iperf operates on port 5201.

Let's configure it on router.

![image](https://github.com/user-attachments/assets/fd7ed96c-f2f9-48ce-9719-ae3280e168ad)

When rules are ready, let's configure QoS rules? based on packet marks.

![image](https://github.com/user-attachments/assets/accfb71f-89d3-4e2c-bd7f-0429e491c6cb)

Let's test configurations.

### Test: File tranfer (`client-1` -> `client-3`)

![image](https://github.com/user-attachments/assets/b885b85e-d237-483e-ac5b-b8e5f77311a4)

![image](https://github.com/user-attachments/assets/3a577708-de67-4422-8a6b-ab7cc583ce40)

Here we can see that limitations works. Note: on first picture receiver has 0 bitrate because I interrupted transfer. This is reason why I inserted 2nd picture with receiver

### Test: default iperf3 benchmark (`client-2` -> `client-4`)

![image](https://github.com/user-attachments/assets/342ee891-04b5-4ab2-bcaf-bb227dace5f7)

Bitrate is near 250K/sec. Rules works!

## 2.6 What is the difference between the QoS rules to traffic allocation and priority-based QoS? Try to set up each of them and show then them. In which tasks of this lab do you use one or the other?

**Traffic Allocation QoS**: This method reserves a fixed or guaranteed portion of bandwidth for specific traffic types or flows, irrespective of their priority levels. It ensures that critical traffic receives a minimum required bandwidth while preventing other traffic from overwhelming the network. This is achieved by setting bandwidth limits for each traffic type. It is ideal for use cases where consistent throughput is essential, such as video conferencing or VoIP.

**Priority-Based QoS**: This approach assigns priority levels to different types of traffic, ensuring that high-priority traffic is processed before lower-priority traffic during network congestion. It enhances network efficiency by dynamically managing traffic based on its importance. Configuration involves setting priority values in queues, where lower values indicate higher priority. This method is well-suited for scenarios requiring flexible traffic management, such as prioritizing real-time gaming over background file downloads.

### Test: Traffic Allocation QoS

I configured different speed, but equal priority.

![image](https://github.com/user-attachments/assets/2aa4189c-66a9-4969-9ad1-6240c4319e5e)

![image](https://github.com/user-attachments/assets/77c5b059-0ae3-4d58-80c4-c46afddd6e2a)

![image](https://github.com/user-attachments/assets/72d1f5fa-9fed-498f-a9c6-428f7f3d6d03)

As we can see, default iperf3 test and file transfer have different bitrate.

### Test: Priority-Based QoS

In this case speed is equal, but priority is different. 

![image](https://github.com/user-attachments/assets/945e946a-66b0-4abd-bd41-a6fa7a5d37a4)

![image](https://github.com/user-attachments/assets/3f2dc01a-a0e1-4c94-8927-7ba5b7bd9169)

![image](https://github.com/user-attachments/assets/057abad3-5f3f-455e-8b4b-6b54c3fce221)

Results are good. File transfer with stict priority has bigger bitrate than default iperf3 test.

## 2.7 Choice and install any tool that you like for bandwidth control/netflow analysis/network control & monitoring. Play around with the network settings and show the different QoS metrics via UI.

The mojority of tools for with GUI are to complex (for example Zabbix, Nagios - general purpose monitoring systems). On the other hand, the are many good CLI tools to Analyze, but they have no UI. I decided to use built in Mikrotik tools, because it is very convenient.

I will use `torch` tool. Using this tool we can see exact bitrate of our rules in test runtime.

iperf3 default:

![image](https://github.com/user-attachments/assets/b64c7368-227c-41a8-8bb4-62ce63e9a74a)

file transfer:

![image](https://github.com/user-attachments/assets/c9324bc6-1eb9-405e-9882-52f3c8cfff32)

## 2.8 Try to answer the question: packet drops can occur even in an unloaded network where there is no queue overflow. In what cases and why does this happen?

### Hardware or software limitations

Packet drops can occur in an unloaded network even when there is no queue overflow due to various underlying factors. One common reason is **hardware or software limitations** in network devices, such as routers or switches. These devices have finite processing capabilities, and if they are overwhelmed by high packet rates or complex operations (e.g., deep packet inspection, encryption, or NAT), they may drop packets despite the network not being congested. This is especially true in low-end or misconfigured devices that lack the resources to handle traffic efficiently.

### Routing Loops or Failures

Asymmetric routing occurs when packets traveling to a destination follow a different path than those returning from it. For example, if a packet is sent out through one route but the response takes an alternate path, and the second router lacks the necessary forwarding or session state, the packet may be dropped. This can lead to communication issues or failures.

### Misconfigurations

Another cause is **misconfigurations** in the network setup. For example, mismatched MTU (Maximum Transmission Unit) sizes between devices can lead to packet fragmentation issues, where packets are dropped because they cannot be properly reassembled. Similarly, incorrect routing tables, firewall rules, or QoS policies can inadvertently cause packets to be discarded even when bandwidth is available. These issues often arise from human error or lack of proper network tuning.

### Protocol-specific behavior

**Protocol-specific behavior** can also result in packet drops. For instance, TCP retransmissions or ICMP messages might be dropped due to security policies or rate-limiting mechanisms. Additionally, certain protocols or applications may generate malformed packets that are discarded by network devices as a safety measure. In such cases, the drops are not due to congestion but rather to the inherent design or operational logic of the network components. Addressing these issues requires careful analysis of device configurations, traffic patterns, and protocol behavior.

### Firewall or Security Filtering:

**Packet filtering** involves the use of firewalls and other security devices to examine and control network traffic based on predefined rules. If a packet fails to comply with these rules—such as having invalid headers, belonging to an unsupported protocol, or being associated with a prohibited service—it will be blocked or dropped to prevent potential security risks.

# Task 3 - QoS verification & packets analysis

## 3.1 How can you check if your QoS rules are applied correctly? List and describe the various methods.

### Queue stats

We can use `queue tree print stats` command to show some statistics of usage of our rules.

![image](https://github.com/user-attachments/assets/95b09814-faa0-42b2-a8ab-e689f37b7a9f)

### Firewall stats

We can also watch stats on firewall tables. In my case is `mangle`.

![image](https://github.com/user-attachments/assets/31edefdd-b07c-40e8-9463-1b405e0e7703)

### Benchmark tools

Tools for benchmark (`iperf3`, that we used earlier or `iftop`) are also useful to determine whether rules work or not.

## 3.2 Try to use Wireshark to see the QoS packets. How does this depend on the number of routers in the network topology?

Let's try to capture some QoS packets. I would like to sniff between `router` and `switch-3`.

![image](https://github.com/user-attachments/assets/07d4aa04-a4ec-4b0e-96ee-01bc2ff843ac)

The size of the network topology, particularly the number of routers, can influence how QoS markings (such as DSCP or IP precedence) are managed. As the number of routers increases, the chances of packet re-marking or drops also rise due to fluctuating network conditions. Tools like Wireshark can be used to monitor these changes. Despite these variations, the fundamental QoS mechanism stays consistent, with each router potentially altering the packet according to its configuration and the traffic conditions it encounters.

# References

- https://iperf.fr/iperf-doc.php
- https://en.wikipedia.org/wiki/Quality_of_service
- https://help.mikrotik.com/docs/spaces/ROS/pages/189497483/Quality+of+Service
