# INR Lab 5

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

