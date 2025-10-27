# Netmaze-Azure-Hybrid-Networking-Project
NetMaze Hybrid Networking Project - An end-to-end Azure projectthat builds a segmented VNet, secures admin access with Bastion, exposes Azure SQL via Private Link, balances web traffic with an internal Load Balancer and connects to a simulated on-prem VNet using a site-to-site VPN. Includes NSGs and Private DNS. 
# What was the aim of the project?

To build a secure, realistic hybrid Azure network that segments workloads, exposes applications privately, and connects a cloud VNet to a simulated on-prem VNet over a site-to-site VPN and then prove it works end-to-end.

# Topics covered 

- Virtual Networking: VNets, CIDR, subnets (WebApp/DB/Admin), GatewaySubnet, AzureBastionSubnet, cross-region (West US / West US 2).

- Security: NSGs (inbound rules + subnet associations), Azure Bastion for RDP/SSH (no public IPs), least-privilege notes for DB.

- Hybrid Connectivity: Virtual Network Gateways (VPN type, SKUs, active-active), Local Network Gateways, S2S VPN (IPsec/IKEv2, PSK), connection status.

- PaaS & Private Access: Azure SQL (server + DB), Private Link/Private Endpoint (private IP only), deny public access (validated via DNS).

- Name Resolution: Private DNS Zones (e.g., mywebapp.local), A records (e.g., web1.mywebapp.local), privatelink DNS + nslookup.

- Traffic & Web Tier: Internal Standard Load Balancer (frontend, backend pool, rule), health probes, NSG probe allowance, IIS serving HTTP (80).

- Operations & Validation: Portal Insights for LB health, Run Command/PowerShell (Install-WindowsFeature -name Web-Server -IncludeManagementTools, Start-Service W3SVC), ping/nslookup checks, VPN Connected.

