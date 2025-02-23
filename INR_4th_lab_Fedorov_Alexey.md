# INR Lab 4

## Done by Fedorov Alexey

---

# Task 1 - Prepare your network topology

## 1.1 In the GNS3 project, select and install a virtual routing solution that you would like to use: Mikrotik (recommended), Pfsense, vyos and so on.

I use MIkrotik, as in previous labs.

## 1.2 Prepare a simple network consisting of at least 3 routers, each one of them has a different subnet, and they should be able to reach each other (for example by a switch/router in the middle or a bus topology). Do not write a static routes between different networks.

I decided to use topology `a`.

![image](https://github.com/user-attachments/assets/75d745e1-f490-4d50-b159-d54c0f025d70)

Let's configure it.

Clients configuration:

![image](https://github.com/user-attachments/assets/6582be89-725c-448c-aed3-73fe1af978bf)

![image](https://github.com/user-attachments/assets/71ec8d75-fa3b-41b5-acfd-c929b614edab)

![image](https://github.com/user-attachments/assets/270db771-ef99-4706-8f93-36e9a1386c42)

![image](https://github.com/user-attachments/assets/f1f535e0-e8d4-4b85-b39c-548a54172d05)

Routers configuration:

![image](https://github.com/user-attachments/assets/ce8f946f-f05b-43cb-b685-2fad78db5e5f)

![image](https://github.com/user-attachments/assets/f671f2fc-1e6a-4c11-89dc-f686485a05fe)

![image](https://github.com/user-attachments/assets/9c19f91a-40fc-493c-a6d0-f72a8352137f)

![image](https://github.com/user-attachments/assets/d5571e91-a065-4821-877f-59295d6d8ae1)

Final topology:

![image](https://github.com/user-attachments/assets/918fcb2d-a056-4113-bbd2-4d602d16f67a)

# Task 2 - OSPF Learning & Configuring

OSPF (Open Shortest Path First) is a dynamic IP routing protocol designed to identify the most efficient route for data packets. Functioning within a single autonomous system, it employs link-state routing, allowing each router to build a comprehensive topology map of the network. By exchanging link-state advertisements, routers collectively construct this map and determine optimal paths using Dijkstra's algorithm. OSPF offers features such as load balancing, rapid convergence, and hierarchical network organization through the use of areas.

How OSPF Works:

- Link-State Routing - Each router builds and maintains a topological map of the network rather than relying on distance-vector metrics. Routers exchange Link-State Advertisements (LSAs) to share network topology information.
- Dijkstra’s Algorithm - OSPF uses Dijkstra’s Shortest Path First (SPF) algorithm to compute the best routes. Every router calculates the shortest path independently based on the network topology.
- Hierarchical Structure - OSPF networks are divided into areas, reducing overhead and improving efficiency. Backbone Area (Area 0) connects all other areas and ensures seamless routing between them.
- Fast Convergence & Load Balancing - OSPF quickly updates routing tables when network changes occur, reducing downtime. It supports Equal-Cost Multi-Path (ECMP), allowing multiple routes with the same cost to be used simultaneously.

## 1.1 Deploy OSPF in your chosen network topology.

I configured OSPF on the routers by creating an OSPF instance with a unique Router ID, setting up a new area with the Area ID `0.0.0.0`, and assigning interfaces to this area. To accomplish this, I used the following commands:

![image](https://github.com/user-attachments/assets/ce4d6add-0aa0-46f8-ae4e-f731533486a0)

![image](https://github.com/user-attachments/assets/997f502c-da57-4811-b688-91f471ff32cf)

![image](https://github.com/user-attachments/assets/fe19518c-3886-4562-93ec-f34099e7f65a)

![image](https://github.com/user-attachments/assets/025dc730-8229-4468-bb31-99bfec673e51)

## 1.2 Which interface you will select as the OSPF router ID and why? 

In OSPF, the Router ID is a distinct 32-bit identifier assigned to each router to uniquely identify it within the OSPF network. This identifier is crucial for maintaining the integrity of the link-state database, preventing conflicts, and ensuring accurate routing decisions. Since the Router ID must be unique, I assign it based on the IP address of the interface that connects my routers. For instance, for routers 1, 2, and 3, I use an IP address from the `192.168.1.0/24` subnet, while for router 4, I assign an IP from the `192.168.15.1/24` subnet.

## 1.3. What is the difference between advertising all the networks VS manual advertising (per interface or per subnet)? Which one is better?

