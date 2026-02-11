Hands-On Demo: Internal Load Balancer Setup (Step-by-Step)
Phase 1: Virtual Network Infrastructure

    Create VNet: Name it Project-VNet.

    Address Space: 10.0.0.0/16.

    Subnets:

        Subnet-Front (10.0.1.0/24) -> For the Client/Test VM.

        Subnet-Back (10.0.2.0/24) -> For Backend Servers & Load Balancer.

Phase 2: Create Backend Web Servers

    Create VM1: Name it Srv-Web-01.

        Subnet: Subnet-Back.

        Public IP: None.

        User Data (Script):
        Bash

        #!/bin/bash
        apt-get update && apt-get install -y nginx
        echo "Response from Server 01" > /var/www/html/index.html

    Create VM2: Name it Srv-Web-02.

        Subnet: Subnet-Back.

        Public IP: None.

        User Data (Script):
        Bash

        #!/bin/bash
        apt-get update && apt-get install -y nginx
        echo "Response from Server 02" > /var/www/html/index.html

Phase 3: Deploy the Internal Load Balancer (ILB)

    Create Resource: Search "Load Balancer" -> Create.

    Type: Internal | SKU: Standard.

    Frontend IP: Assign a Private IP from Subnet-Back.

    Backend Pool: Add Srv-Web-01 and Srv-Web-02.

    Health Probe: Create a TCP probe on Port 80.

    Load Balancing Rule:

        Protocol: TCP.

        Port: 80.

        Backend Port: 80.

Phase 4: Verification

    Create Client VM: Create a small VM in Subnet-Front with a Public IP (to allow you to login).

    Test Connection: * SSH into the Client VM.

        Run curl <Internal-Load-Balancer-Private-IP>.

        Observe the response toggle between "Server 01" and "Server 02".