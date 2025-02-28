# Lab 3

## Done by Fedorov Alexey

---

# Task 1 - Preparation

## 1.{a,b,c,d} Select a virtual switching solution that you would like to try. For example (Mikrotik, vyos, Cumulus VX).

I've read instructions. Decided to use Mikrotik. Made a snapshot.

# Task 2 - VLANs

## 2.a Change the topology of your network to as follows, and make the necessary configurations.

I fully prepared topology:

![image](https://github.com/user-attachments/assets/3811d487-f267-412e-bb97-4d5a890c0199)

## 2.b Configure the switches and make sure you have connectivity between the hosts.

I configured switches, let's ensure connectivity:

`Web` to All:

![image](https://github.com/user-attachments/assets/f74987ad-b6e9-4bcf-ad76-8697799f8079)

`HR` to All:

![image](https://github.com/user-attachments/assets/e96a2755-507c-4ac3-b0ee-b17ccfc8b324)

## 2.c How do VLANs work at a packet level? What are the two main protocols used for this?

### VLAN workflow algorithm:

**Tagging (IEEE 802.1Q)** - When a packet enters a VLAN switch, a VLAN tag is added to the Ethernet frame. The tag includes:

- VLAN ID (**VID**, 12 bits) - Identifies the VLAN (1–4094).

- Priority Code Point (**PCP**, 3 bits) -  Used for QoS (Quality of Service).

- Drop Eligible Indicator (**DEI**, 1 bit) - Marks frames eligible for dropping under congestion.

- Tag protocol identifier (**TPID**, 16 bits) - A field to identify the frame as an IEEE 802.1Q-tagged frame.

![image](https://github.com/user-attachments/assets/f044a6c0-116b-4cc2-bbdf-a51d00d6d757)

**Forwarding** - Switches use the VLAN ID to forward the packet only to ports that belong to the same VLAN.

**Untagging** - When the packet leaves the VLAN-aware switch toward a device that doesn’t understand VLAN tags, the tag is removed.

### Two protocols that is used for VLAN:

- IEEE 802.1Q - The standard protocol for VLAN tagging, which inserts a 4-byte VLAN tag into the Ethernet frame.

- ISL (Inter-Switch Link) - A Cisco-proprietary protocol for VLAN tagging, which encapsulates the entire Ethernet frame with a new header and trailer.

## 3.d What is the Native VLAN?

The **Native VLAN** is a feature in the **802.1Q standard** that assigns a VLAN to handle untagged traffic on a switch. By default, this is **VLAN 1**, but on some switches, it can be reconfigured to another VLAN. The switch automatically assigns the Native VLAN tag to any incoming untagged frames. However, port-based VLANs have a limitation: only one VLAN can process all untagged traffic.

## 3.e Configure the VLANs on the switches to isolate the two virtual networks as follow

I configured vlan as on [picture](https://i.imgur.com/zyoR0V1.png).

![image](https://github.com/user-attachments/assets/a9332fa2-3d9c-44ec-96f1-d24c1805805d)

Admistration configuration for example.

![image](https://github.com/user-attachments/assets/0b99a6c5-df15-4e29-9c14-256868752688)

## 3.f Ping between ITManager and HR, do you have replies? Ping between ITManager and Management, do you have replies? Can you see the VLAN ID in Wireshark?

Let's test configuration. 

### Test: Ping between ITManager and HR (HR -> ITManager)

![image](https://github.com/user-attachments/assets/ffe697ba-7f1f-4354-bf11-227854f198d1)

There no replies, moreover, ITManager not reachable to HR

To see VLAN tag, we need to capture packet betweeb routers? because router adds this header. I will sniff Administration to Internal connection.

![image](https://github.com/user-attachments/assets/f7b7424f-f12e-4798-9d29-b3af76b041ea)

As we can see? ARP frames had IEEE 802.1Q ID = 3 of VLAN.

### Test: Ping between ITManager and Management (ITManager -> Management) 

![image](https://github.com/user-attachments/assets/c156776e-baa7-4c2b-be45-584300b90430)

Connection is present between ITManager and Management.

As we can see in Wireshark, ICMP requests were replied. 

![image](https://github.com/user-attachments/assets/07187379-dc5f-4664-8747-55154b544e05)

## 3.g Configure Inter-VLAN Routing between Management VLAN and HR VLAN and Show that you can now ping between them.

If configured Inter-VLAN on microtic router. I added VLANs on physical interface and connected IP Addresses.

![image](https://github.com/user-attachments/assets/5b0925ef-9ac1-45d9-a8ff-9d54782d84c3)

Next step, I reconfigured PCs IPs.

![image](https://github.com/user-attachments/assets/827ff0e9-301b-4764-ba57-3604e433e366)

### Test: Ping between HR and Management (HR -> Management)

![image](https://github.com/user-attachments/assets/51045ef3-d6d1-466e-9740-013ce7156f77)

It works!
