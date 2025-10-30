## 1) Azure Virtual Network Setup

Provisioned NetMaze-Vnet1 in West US using the address space 10.0.0.0/16 and segmented the network into three application subnets: WebApp-Subnet (10.0.0.0/24), Database-Subnet (10.0.1.0/24), and Admin-Subnet (10.0.2.0/24).

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image1.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image2.png?raw=true)



## 2) On-Premises Network Simulation

To simulate an on-prem environment, I created NetMaze-Vnet2 in West US 2 with the address space 10.1.0.0/16.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image3.png?raw=true)

(This configuration is revisited and expanded upon in Step 9 Hybrid Connectivity Site-to-Site VPN)


## 3) Secure Connectivity (Gateway in VNet1)

I added a GatewaySubnet (10.0.3.0/24) to NetMaze-VNet1.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image4.png?raw=true)

Deployed route-based VPN gateway NetMaze-VPN-Gateway with two public IPs (MainGatewayIP, SecondaryGatewayIP).

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image5.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image6.png?raw=true)



## 4) Resource Deployment in VNet1

I deployed Maze-VM1 in the WebApp subnet to serve as a web tier VM.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image7.png?raw=true)

I also created an Azure SQL Database named NetMaze-DB on server mazeserver-db. 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image8.png?raw=true)

A Private Endpoint named maze-endpoint was added in the Database subnet and integrated with Private DNS.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image9.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image10.png?raw=true)


## 5) Network Access Control (NSGs)

I configured two network security groups called NSG-WebApp and NSG-Database WebApp-Subnet and Database-Subnet. 

For demonstration, I allowed inbound HTTP (TCP/80) and HTTPS (TCP/443) to both NSG's and associated each NSG to its respective subnet.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image11.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image12.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image13.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image14.png?raw=true)
 
![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image15.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image16.png?raw=true)



## 6) Secure Administrative Access (Bastion)

I added AzureBastionSubnet (10.0.4.0/26) 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image17.png?raw=true)

Deployed Azure Bastion NetMaze-Bastion. 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image18.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image19.png?raw=true)

I then connected to Maze-VM1 over RDP via Bastion without exposing any public IPs.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image20.png?raw=true)
![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image21.png?raw=true)



## 7) Private Access to Azure PaaS (Private Link)

I created a second SQL private endpoint netmaze-sql-private-endpoint in the Database subnet. Note: My SQL Database can now only be accessed via its Private Endpoint inside the Vnet using the private IP (10.0.1.5) or FQDN; public internet access is blocked. 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image22.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image23.png?raw=true)

The NIC shows the private IP 10.0.1.5. 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image24.png?raw=true)

Ran ns lookup for mazeserver-db.database.windows.net resolves to the privatelink FQDN and then to 10.0.1.5, confirming private access.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image25.png?raw=true)


## 8) DNS & Internal Load Balancing

I created a private DNS zone mywebapp.local and linked it to NetMaze-VNet1. 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image26.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image27.png?raw=true)

I added an A record web1.mywebapp.local → 10.0.0.4 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image28.png?raw=true)

Verified resolution from the VM by pinging web1.mywebapp.local response resolved to the correct private IP from the private DNS mywebap.local.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image29.png?raw=true)

I deployed an Internal Standard Load Balancer WebApp-LoadBalancer with frontend 10.0.0.10, a backend pool containing Maze-VM1 and Maze-VM2, a TCP/80 rule, and an HTTP health probe. 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image30.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image31.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image32.png?raw=true)



I created an inbound security rule called Allow-LB-Probe this rule lets the Load Balancer connect my VM on port 80 so it can check if the VM is healthy.


![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image33.png?raw=true)

I installed IIS on both VMs and started the web service so VMs listen on port 80; the load balancer health probe now sees both VMs as healthy.


![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image34.png?raw=true)


![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image35.png?raw=true)


## 9) Hybrid Connectivity (Site-to-Site VPN)

I added a GatewaySubnet (10.1.0.0/24) to NetMaze-VNet2


![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image38.png?raw=true)

Created NetMaze-VNET2-Gateway in West US 2 and in NetMaze-VNET2.


![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image39.png?raw=true)


![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image40.png?raw=true)

I created two Local Network Gateways so each side knows the other’s public IP and address space. 

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image41.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image42.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image43.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image44.png?raw=true)

I created two site-to-site (IPsec/IKEv2) connections. The first connection links the NetMaze-VPN-Gateway to NetMaze-VNET2-LNG, while the second connects the NetMaze-VNET2-Gateway to NetMaze-VNET1-LNG. They both use a shared key (PSK) for authentication.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image45.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image46.png?raw=true)

Both connections show as connected.

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image47.png?raw=true)

![image alt](https://github.com/davidyemo/Netmaze-Azure-Hybrid-Networking-Project/blob/main/images/project-image48.png?raw=true)

In summary, this setup creates a secure Site-to-Site (VNet-to-VNet) VPN tunnel between two VNets allowing encrypted communication and data flow between cloud resources and my simulated on-prem network.






