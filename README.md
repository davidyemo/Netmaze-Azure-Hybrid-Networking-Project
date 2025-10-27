# Netmaze Azure Hybrid Networking Project
NetMaze Hybrid Networking Project - An end-to-end Azure projectthat builds a segmented VNet, secures admin access with Bastion, exposes Azure SQL via Private Link, balances web traffic with an internal Load Balancer and connects to a simulated on-prem VNet using a site-to-site VPN. Includes NSGs and Private DNS. 

# 🎯 Goal of the Project

The goal of the NetMaze Azure Networking Project is to design and deploy a secure and interconnected Azure network environment that simulates a real-world enterprise setup. The project focuses on building end-to-end connectivity between two virtual networks using VPN Gateway, enabling secure remote access with Bastion, and ensuring controlled communication through NSGs and Private Endpoints.

# 🧩 What Problem It Solves / Demonstrates

The project effectively simulates how enterprise networks achieve secure hybrid connectivity, segregated network zones and controlled service access within Azure.

This project demonstrates how to:

- Connect separate Azure networks (VNets) securely over the internet using VPN Gateway.

- Enable remote management without exposing public IPs through Azure Bastion.

- Securely access Azure PaaS services via Private Endpoints instead of public endpoints.

- Implement high availability using a Load Balancer to distribute traffic between backend VMs.

- Enforce segmentation and security with Network Security Groups (NSGs).

