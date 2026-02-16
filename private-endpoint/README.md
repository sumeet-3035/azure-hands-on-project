n this demo, we will lock down an Azure Storage Account so that it is invisible to the internet and only accessible via a private IP address inside your Virtual Network.
The Lab Architecture

    A Virtual Network (VNet): Your private sandbox.

    A Virtual Machine (VM): Our "Jumpbox" to test the connection.

    A Storage Account: The resource we want to hide.

    A Private Endpoint: The "Network Interface" that gives our storage a private IP.

    Private DNS Zone: The magic that tells your VM "don't go to the internet for this URL, go to this private IP."

Step 1: Create the Network and Test VM

    Create a VNet: Name it Lab-VNet with address space 10.0.0.0/16.

    Create a Subnet: Name it Workload-Subnet (10.0.1.0/24).

    Deploy a VM: Create a small Windows or Linux VM in that subnet. This will be your "client."

Step 2: Create the Storage Account

    Create a standard Storage Account (e.g., mystorageaccount2026).

    Crucial Step: Go to the Networking tab during creation (or after).

    Set Public network access to Disabled.

        Note: If you try to upload a file from your local computer's browser now, it will fail. This proves the "wall" is up.

Step 3: Configure the Private Endpoint

    In the Storage Account menu, go to Networking > Private endpoint connections.

    Click + Private Endpoint.

    Resource: Select blob as the target sub-resource.

    Configuration: Point it to your Lab-VNet and Workload-Subnet.

    Private DNS Integration: Select Yes. This is vital; it creates a zone called privatelink.blob.core.windows.net.

Step 4: The Connectivity Test

Login to your VM via RDP or SSH and perform these two tests:
Test A: The DNS Resolution

Run this command in the terminal:
nslookup mystorageaccount2026.blob.core.windows.net

    What you should see: The FQDN should resolve to a 10.0.1.x address.

    What it means: The Private DNS zone is working. Instead of pointing to a public IP, Azure is routing you to the internal endpoint.

Test B: The Data Access

Try to reach the storage service over the network:

    Windows (PowerShell): Test-NetConnection mystorageaccount2026.blob.core.windows.net -Port 443

    Linux: telnet mystorageaccount2026.blob.core.windows.net 443

If it says TcpTestSucceeded : True, you have successfully bypassed the public internet to reach your data.
