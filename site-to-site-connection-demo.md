# Site-to-Site VPN Connection Between Azure and AWS Demo

## Architecture Diagram:

![draw](https://user-images.githubusercontent.com/66474973/193271686-5be132db-0109-4ae1-acbe-1709061e1548.png)

### Step 1: Configuring Azure
 1. Crate a resource group on Azure to deploy the resources on that
 ```bash
 Resource Group Name: rg-azure-aws
 Region: East-US
 ```
 2. Create Virtual Network
 ```bash
 Resource Group Name: rg-azure-aws
 Region: East-US
 VNet Name: vnet-azure
 VNet IPv4 Address Space: 172.10.0.0/16
 Subnet Name: subnet-01
 Subnet IPv4 Address Space: 172.10.1.0/24
 ```
 3. Create the VPN Gateway
 ```bash
 VPN Gateway Name: vpn-azure-aws
 Region: East-US
 Gateway Type: VPN
 SKU: VpnGw1
 Generation: Generation 1
 Virtual Network: vnet-azure
 Public IP Address: pip-vpn-azure-aws
 Public IP Address Type: Basic
 Assignment: Dynamic
 Enable active-active mode: Disabled
 Configure BGP: Disabled
 ```
### Configuring AWS
 4. Create the Virtual Private Cloud (VPC) in AWS
 ```bash
 Name: my-vpc-01
 IPv4 CIDR: 10.10.0.0/16
 ```
 5. Create a subnet inside the VPC (Virtual Network)
 ```bash
 Name: my-subnet-01
 VPC Name: my-vpc-01
 VPC IPv4 CIDR: 10.10.0.0/16
 IPv4 CIDR: 10.10.1.0/24
 ```
 6. Create a customer gateway pointing to the Public IP Address of Azure VPN Gateway
 ```bash
 IP address: Public IP Address of Azure VPN Gateway
 Rest keep everything as default
 ```
 7. Create the Virtual Private Gateway then attach to the VPC
 ```bash
 Name: vpg-aws-azure
 ```
 8. Create a site-to-site VPN Connection
 ```bash
 Name: vpn-aws-azure
 Target gateway type: Virtual private gateway (Select your Virtual private gateway created in 7)
 Customer gateway: Existing (Select your VCustomer gateway created in 6)
 Routing options: Static
 Static IP prefixes: 172.10.1.0/24
 Leave rest of them as default
 ```
 9. Download the configuration file
 ```bash
 Vendor: Generic
 Platform: Generic
 Software: Vendor Agnostic
 In this configuration file you will note that there are the Shared Keys and the Public Ip Address for each of one of the two IPSec tunnels created by AWS.
 ```
### Connecting Azure and AWS
 
 10. Create the Local Network Gateway in Azure
 ```bash
 Name: lng-azure-aws
 Resource Group Name: rg-azure-aws
 Region: East-US
 IP address: Get the Outside IP address from the configuration file downloaded in 9.
 Address Space(s): 10.10.0.0/16
 ```
 11. Create the connection on the Virtual Network Gateway in Azure
 ```bash
 Name: connection-azure-aws
 Connection Type: Site-to-Site
 Local Network Gateway: Select the Local Network Gateway which you created in 10.
 Shared Key: Get the Shared Key from the configuration file downloaded in 9.
 Wait till the Connection Status changes to - Connected
 In the same way, check in AWS Console wheather the 1st tunnel of Virtual Private Gateway UP.
 ```
 12. Create Internet Gateway and Attach it to VPC in AWS:\
 ```bash
 Name: my-internet-gateway
 ```
 13. Now let's edit the route table associated with our VPC
 ```bash
 Add the route to Azure subnet through the Virtual Private Gateway
 Destination: 172.10.1.0/24
 Target: Virtual Private Gateway that we created.
 also add,
 Destination: 0.0.0.0/0
 Target: Internet Gateway that we created in 12.
 ```
14. Create VMs in both Azure and AWS and Test the connection.
