You are deploying a web application. You need at least 2 VMs running at all times. If the CPU load across the fleet goes over 70%, Azure should automatically add a 3rd VM. If a VM crashes, Azure should replace it.
Step 1: Resource Group & Basics

    Sign in to the Azure Portal.

    Search for Virtual machine scale sets and click Create.

    Project Details:

        Subscription: Select your subscription.

        Resource group: Create new (e.g., RG-VMSS-Demo).

    Instance Details:

        Name: MyWebScaleSet.

        Region: (US) East US (or your preferred region).

        Orchestration: Flexible (This is the best choice for 2026 as it simplifies management of individual VMs).

        Image: Ubuntu Server 22.04 LTS - Gen2.

        Size: Standard_B1s (Cheap and perfect for testing).

Step 2: Configure Networking & Load Balancer

A Scale Set needs a "front door" so users don't have to guess which VM IP to use.

    Go to the Networking tab.

    Virtual network: Create new (or use default).

    Load balancing:

        Check "Use an Azure load balancer."

        Create a load balancer: Click "Create new."

        Name: MyWebLB.

        Type: Public.

        Protocol: TCP/80 (HTTP).

    Inbound Ports: Select Allow selected ports and choose HTTP (80) and SSH (22).

Step 3: Autoscale Rules (The "Scaling" Tab)

This is the "brain" of your Scale Set.

    Go to the Scaling tab.

    Autoscale: Select Custom.

    Minimum number of instances: 2.

    Maximum number of instances: 5.

    Scale out rule (Adding):

        Metric: Percentage CPU.

        Operator: Greater than.

        Threshold: 70%.

        Duration: 5 minutes.

        Operation: Increase count by 1.

    Scale in rule (Removing):

        Threshold: Less than 30%.

        Operation: Decrease count by 1.

Step 4: The "Bootstrap" Script (Advanced Tab)

We want these VMs to be web servers the moment they are born.

    Go to the Advanced tab.

    Look for the Custom data text box. Paste this script:
    Bash

    #!/bin/bash
    sudo apt-get update
    sudo apt-get install -y apache2
    # This line writes the VM's unique name to the index page
    echo "<h1>Welcome to the Scale Set. I am VM: $(hostname)</h1>" | sudo tee /var/www/html/index.html
    sudo systemctl restart apache2

    Click Review + Create, then Create.

Step 5: Verification (The "Showcase")

Wait 3–5 minutes for deployment, then perform these checks:
1. The Multi-VM Check

Go to the Instances menu on the left. You should see two VMs (e.g., MyWebScaleSet_0 and MyWebScaleSet_1) in the Running state.
2. The Load Balancer Test

    Go to the Load Balancer resource (MyWebLB).

    Copy the Frontend IP address.

    Open a new browser tab and paste that IP.

    You should see: "Welcome to the Scale Set. I am VM: [Name]".

    Refresh the page multiple times. You should see the VM name change as the Load Balancer distributes traffic between your two instances.

3. The "Self-Healing" Test

    Go back to the Instances tab in the VMSS.

    Select one VM and click Delete.

    Refresh the page after a minute. You will see that Azure has automatically started "Creating" a new VM to replace the one you just deleted to maintain your "Minimum of 2" rule.
