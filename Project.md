## 1) Azure Virtual Network Setup

Provisioned NetMaze-Vnet1 in West US using the address space 10.0.0.0/16 and segmented the network into three application subnets: WebApp-Subnet (10.0.0.0/24), Database-Subnet (10.0.1.0/24), and Admin-Subnet (10.0.2.0/24).

Image 1:

Image 2:



## 2) On-Premises Network Simulation

To simulate an on-prem environment, I created NetMaze-Vnet2 in West US 2 with the address space 10.1.0.0/16.

Image 3:


## 3) Secure Connectivity (Gateway in VNet1)

I added a GatewaySubnet (10.0.3.0/24) to NetMaze-VNet1.

Image 4:

Deployed route-based VPN gateway NetMaze-VPN-Gateway with two public IPs (MainGatewayIP, SecondaryGatewayIP).

Image 5:

Image 6:



## 4) Resource Deployment in VNet1

I deployed Maze-VM1 in the WebApp subnet to serve as a web tier VM.

Image7:

I also created an Azure SQL Database named NetMaze-DB on server mazeserver-db. 

Image 8:

A Private Endpoint named maze-endpoint was added in the Database subnet and integrated with Private DNS.

Image 9:

Image 10:


## 5) Network Access Control (NSGs)

I configured two network security groups called NSG-WebApp and NSG-Database WebApp-Subnet and Database-Subnet. 

For demonstration, I allowed inbound HTTP (TCP/80) and HTTPS (TCP/443) to both NSG's and associated each NSG to its respective subnet.

Image 11:

 Image 12:

Image 13:

 Image 14:
 
Image 15:

 Image 16:



## 6) Secure Administrative Access (Bastion)

I added AzureBastionSubnet (10.0.4.0/26) 

Image 17:

Deployed Azure Bastion NetMaze-Bastion. 

Image 18:

Image 19:

I then connected to Maze-VM1 over RDP via Bastion without exposing any public IPs.

Image 20:
Image 21:

Screens: Image 17, Image 18, Image 19, Image 20, Image 21
(Refs: images/image-17.png – image-21.png)

## 7) Private Access to Azure PaaS (Private Link)

I created a second SQL private endpoint netmaze-sql-private-endpoint in the Database subnet. Note: My SQL Database can now only be accessed via its Private Endpoint inside the Vnet using the private IP (10.0.1.5) or FQDN; public internet access is blocked. 

Image 22:

Image 23:

The NIC shows the private IP 10.0.1.5. 

Image 24:

Ran ns lookup for mazeserver-db.database.windows.net resolves to the privatelink FQDN and then to 10.0.1.5, confirming private access.

Image 25:


## 8) DNS & Internal Load Balancing

I created a private DNS zone mywebapp.local and linked it to NetMaze-VNet1. 

Image 26:

Image 27:

I added an A record web1.mywebapp.local → 10.0.0.4 

Image 28:

Verified resolution from the VM by pinging web1.mywebapp.local response resolved to the correct private IP from the private DNS mywebap.local.

Image 29:

I deployed an Internal Standard Load Balancer WebApp-LoadBalancer with frontend 10.0.0.10, a backend pool containing Maze-VM1 and Maze-VM2, a TCP/80 rule, and an HTTP health probe. 

Image 30:

Image 31:

Image 32:



I created an inbound security rule called Allow-LB-Probe this rule lets the Load Balancer connect my VM on port 80 so it can check if the VM is healthy.

Image 33:

I installed IIS on both VMs and started the web service so VMs listen on port 80; the load balancer health probe now sees both VMs as healthy.

Image 34:

Image 35


## 9) Hybrid Connectivity (Site-to-Site VPN)

I added a GatewaySubnet (10.1.0.0/24) to NetMaze-VNet2

Image 38:

Created NetMaze-VNET2-Gateway in West US 2 and in NetMaze-VNET2.

Image 39:

Image 40:

I created two Local Network Gateways so each side knows the other’s public IP and address space. 

Image 41:

Image 42:

Image 43:

Image 44:

I created two site-to-site (IPsec/IKEv2) connections. The first connection links the NetMaze-VPN-Gateway to NetMaze-VNET2-LNG, while the second connects the NetMaze-VNET2-Gateway to NetMaze-VNET1-LNG. They both use a shared key (PSK) for authentication.

Image 45:

Image 46:

Both connections show as connected.

Image 47:

Image 48:




