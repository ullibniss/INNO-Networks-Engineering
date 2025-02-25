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

