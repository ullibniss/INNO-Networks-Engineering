# INR Lab 4: OSPF

## Completed by Fedorov Alexey (tg: @ullibniss)

---

# Task 1 - Prepare your network topology

## 1.1 In the GNS3 project, select and install a virtual routing solution that you would like to use: Mikrotik (recommended), Pfsense, vyos and so on.

I am using MikroTik, as in previous labs.

## 1.2 Prepare a simple network consisting of at least 3 routers, each one of them has a different subnet, and they should be able to reach each other (for example by a switch/router in the middle or a bus topology). Do not write a static routes between different networks.

I decided to use topology `a`.

![image](https://github.com/user-attachments/assets/75d745e1-f490-4d50-b159-d54c0f025d70)

Let's configure it.

Clients configuration:

![image](https://github.com/user-attachments/assets/f0e9b078-2473-4f0b-a6dc-628a51266114)

![image](https://github.com/user-attachments/assets/ecaa1eec-55da-492c-b4c0-1cbb4e300046)

![image](https://github.com/user-attachments/assets/ec018e1d-6ffc-4487-9113-1f8d9630c394)

![image](https://github.com/user-attachments/assets/65c50354-b993-43a1-ae9e-04d779d8b815)

Routers configuration:

![image](https://github.com/user-attachments/assets/ce8f946f-f05b-43cb-b685-2fad78db5e5f)

![image](https://github.com/user-attachments/assets/f671f2fc-1e6a-4c11-89dc-f686485a05fe)

![image](https://github.com/user-attachments/assets/9c19f91a-40fc-493c-a6d0-f72a8352137f)

![image](https://github.com/user-attachments/assets/d5571e91-a065-4821-877f-59295d6d8ae1)

Final topology:

![image](https://github.com/user-attachments/assets/918fcb2d-a056-4113-bbd2-4d602d16f67a)

# Task 2 - OSPF Learning & Configuring

**OSPF** (Open Shortest Path First) is a dynamic IP routing protocol designed to identify the most efficient route for data packets. Functioning within a single autonomous system, it employs link-state routing, allowing each router to build a comprehensive topology map of the network. By exchanging link-state advertisements, routers collectively construct this map and determine optimal paths using Dijkstra's algorithm. OSPF offers features such as load balancing, rapid convergence, and hierarchical network organization through the use of areas.

How OSPF Works:

- **Link-State Routing** - Each router builds and maintains a topological map of the network rather than relying on distance-vector metrics. Routers exchange Link-State Advertisements (LSAs) to share network topology information.
- **Dijkstra’s Algorithm** - OSPF uses Dijkstra’s Shortest Path First (SPF) algorithm to compute the best routes. Every router calculates the shortest path independently based on the network topology.
- **Hierarchical Structure** - OSPF networks are divided into areas, reducing overhead and improving efficiency. Backbone Area (Area 0) connects all other areas and ensures seamless routing between them.
- **Fast Convergence & Load Balancing** - OSPF quickly updates routing tables when network changes occur, reducing downtime. It supports Equal-Cost Multi-Path (ECMP), allowing multiple routes with the same cost to be used simultaneously.

## 2.1 Deploy OSPF in your chosen network topology.

I configured OSPF on the routers by creating an OSPF instance with a unique Router ID, setting up a new area with the Area ID `0.0.0.0`, and assigning interfaces to this area. To accomplish this, I used the following commands:

![image](https://github.com/user-attachments/assets/6adf2393-a98e-42b4-87fc-c6df49289344)

All routers OSPF configurations:

![image](https://github.com/user-attachments/assets/adf7bd4b-96f3-408b-89ed-dfa975b6d731)

![image](https://github.com/user-attachments/assets/575f2a80-0a0f-402f-a470-18ac0108a0c3)

![image](https://github.com/user-attachments/assets/39612738-0858-4061-be5d-d6c679f3d364)

![image](https://github.com/user-attachments/assets/adcc57c3-8ab9-4608-8427-c9cb47f94f91)

Let's check whether it works:

![image](https://github.com/user-attachments/assets/e8775473-c9f7-4618-8d27-01c2f2edad14)

We can connect to any client from `client-1`. Everything works!

## 2.2 Which interface you will select as the OSPF router ID and why? 

In OSPF, the Router ID is a distinct 32-bit identifier assigned to each router to uniquely identify it within the OSPF network. This identifier is crucial for maintaining the integrity of the link-state database, preventing conflicts, and ensuring accurate routing decisions. Since the Router ID must be unique, I assign it based on the IP address of the interface that connects my routers. For instance, for routers 1, 2, and 3, I use an IP address from the `192.168.1.0/24` subnet, while for router 4, I assign an IP from the `192.168.15.1/24` subnet.

## 2.3. What is the difference between advertising all the networks VS manual advertising (per interface or per subnet)? Which one is better?

In OSPF, choosing between advertising all networks and manually specifying individual networks depends on network design, administrative control, and routing efficiency goals.  

### Advertising All Networks  
This approach allows OSPF to automatically include all connected interfaces with an IP address in the routing process. It is usually configured using a wildcard mask that covers all subnets.  

**Advantages:**  
- Automatically advertises new networks/interfaces as they are added, without requiring reconfiguration.  
- Better for smaller, less complex networks where strong control is unnecessary.  

**Disadvantages:**  
- May include unnecessary routes if some networks or interfaces do not need OSPF advertisement.  
- Increases OSPF traffic and CPU load since all interfaces participate.  

### Manual Advertising  
This method involves specifying each network individually in OSPF, allowing control over which interfaces and subnets are included.  

**Advantages:**  
- Reduces unnecessary OSPF entries by advertising only required networks, potentially improving security.   
- More suitable for larger, segmented networks where isolation of certain interfaces is needed.  

**Disadvantages:**  
- Requires manual configuration for each network or subnet and must be updated when changes occur.  
- Higher risk of errors or omissions due to the manual setup process.  

In general, manual advertising is preferred for scalability and security, while advertising all networks is ideal for simpler environments where ease of management is the priority.

## 2.4 If you have a static route in a router, how can you let your OSPF neighbors know about it? Approve and show it on practice.

To inform OSPF neighbors about a static route, it must be redistributed into OSPF. Redistribution enables routes obtained from other sources, such as static routes, to be shared within the OSPF network, allowing OSPF neighbors to learn and utilize them.

I refactored the network topology:

![image](https://github.com/user-attachments/assets/c403f4b1-f178-4a0b-95fc-74ba78b9ed3b)

Then, I prepared `client-5`.

![image](https://github.com/user-attachments/assets/89510545-6a64-4a56-b655-34923c1f72bd)

Prepared `router-1` and `router-5`:

`router-1`
![image](https://github.com/user-attachments/assets/23f44abe-60dc-4a92-812e-78c27a4d2bad)

`router-5`
![image](https://github.com/user-attachments/assets/127729ea-d620-4b63-8946-d98b5e12372b)

I added a static route on `router-1` to reach `client-5`.

![image](https://github.com/user-attachments/assets/0627e243-839c-4e2a-bcc0-e2df57b6bbe6)

After that, I enabled the redistribution of static routes in the OSPF preferences of `router-4`.

![image](https://github.com/user-attachments/assets/1b80a836-1976-429c-8614-2ac66e261942)

I also set `ether1` on `router-5` as the default interface.

![image](https://github.com/user-attachments/assets/c937ff17-8288-4f32-865f-2a0a9fc6cc56)

Let's check connectivity.

![image](https://github.com/user-attachments/assets/95753fa7-25f3-4546-8558-df8a63ffe066)

Everything is good! The static routes were redistributed successfully.

## 2.5 Enable OSPF with authentication between the neighbors and verify it.

OSPF authentication is a security mechanism that protects OSPF communication between routers by allowing only authorized routers to share routing information. This is achieved by setting up a password or key on OSPF-enabled interfaces, which is then included in OSPF Hello packets.

For authentication, I used the MD5 method and applied it on all the border interfaces of the routers with the following command:

![image](https://github.com/user-attachments/assets/a9e2fe29-1630-4bcd-8277-db22ae31119a)

Moreover, we need protection only on template 1, because we want to turn off authentication between the routers.

Here are the configurations for all routers.

![image](https://github.com/user-attachments/assets/3aaae106-64a6-4cc0-a284-cd7ceb680d9d)

![image](https://github.com/user-attachments/assets/b69d5e04-75c2-44de-87be-4aeedbe149b2)

![image](https://github.com/user-attachments/assets/edf56a28-2185-4441-96db-b9f2bdfa6f00)

![image](https://github.com/user-attachments/assets/16be38f2-6bcf-4d23-b1f7-3d2840fa3fa2)

To confirm that the system is functioning correctly, I checked the neighbor status on `router-3`. The output shows a state of **"Full,"** which signifies that the OSPF adjacency has been successfully established and the routers are fully synchronized.

![image](https://github.com/user-attachments/assets/417d2d5b-acb4-4e76-9a71-c2df2c3a2c5e)

# Task 3 - OSPF Verification

## 3.1 How can you check if you have a full adjacency with your router neighbor?

To confirm this, I need to ensure that the OSPF neighbor relationship has reached the **"Full"** state. As mentioned in task 2.5, the **"Full"** state signifies that OSPF routers have successfully exchanged their link-state information and are fully synchronized. For instance, I checked the neighbor status on `router-4`, and the state parameter shows a value of **"Full"**.

![image](https://github.com/user-attachments/assets/a9da0255-88da-4352-821c-c89b99f14239)

## 3.2 How can you check in the routing table which networks did you receive from your neighbors?

To check the routes received from OSPF neighbors, we use the command:

```bash
/ip route print where ospf
```  

This command displays all routes that were learned through OSPF, allowing us to verify connectivity and routing updates within the network. Let's try it on `router-2`.

![image](https://github.com/user-attachments/assets/30534d28-3949-4c9b-9101-dfc1d83bd8b5)

## 3.3 Use traceroute to verify that you have a full OSPF network.

Let's test the network with `traceroute`. I will check the route from `router-1` to `client-4`.

![image](https://github.com/user-attachments/assets/fc777275-c533-4638-ba7c-0050a09d7d65)

## 3.4 Which router is selected as DR and which one is BDR ?

**Designated Router (DR)** is responsible for creating and distributing Link-State Advertisements (LSAs) within a broadcast network segment. It helps minimize OSPF traffic by ensuring that only the DR and **Backup Designated Router (BDR)** exchange LSAs with other routers, while the remaining routers communicate solely with the DR and BDR. The router with the highest Router ID on the segment is typically elected as the DR. 

**BDR** serves as a backup, passively listening to all LSAs and maintaining a copy of the Link-State Database (LSDB). If the DR fails, the BDR automatically assumes its role, ensuring network stability.

To determine the DR and BDR, we need to display the neighbor information. In my network, the **Designated Router (DR)** is **192.168.1.12**, and the **Backup Designated Router (BDR)** is **192.168.1.13**. Additionally, for the subnet connecting `router-3` and `router-4`, the DR is **192.168.15.2**, while the BDR is **192.168.15.1**.

![image](https://github.com/user-attachments/assets/563ecb44-2e06-4254-a3b6-d6b95e1eb673)

## 3.5 Check what is the cost for each network that has been received by OSPF in the routing table.

We can see **Distance** parameter in routes table. In MikroTik OSPF, the **Distance** parameter represents the **administrative distance**, with a default value of **110** for OSPF. It is used to determine the preferred route when multiple routing options exist, with the router selecting the route that has the lowest distance value.

![image](https://github.com/user-attachments/assets/2fe7435a-260b-4d26-8f46-83066af06e8a)

## References

- https://help.mikrotik.com/docs/spaces/ROS/pages/9863229/OSPF
- https://wiki.mikrotik.com/Manual:OSPF-examples
- https://www.reddit.com/r/mikrotik/comments/qnzq23/trying_to_learn_it_but_cant_get_ospf_working/
