2. Hands-On Demo: Step-by-Step
Phase 1: Create the Two Worlds

    VNet-Alpha:

        Address space: 10.1.0.0/16

        Subnet: default (10.1.0.0/24)

    VNet-Beta:

        Address space: 10.2.0.0/16

        Subnet: default (10.2.0.0/24)

Phase 2: Deploy the "Residents" (VMs)

    VM-Alpha: Create in VNet-Alpha.

        Give it a Public IP (so you can SSH into it) or use Bastion from your previous project!

        Note its Private IP (likely 10.1.0.4).

    VM-Beta: Create in VNet-Beta.

        Does NOT need a Public IP.

        Note its Private IP (likely 10.2.0.4).

Phase 3: Build the Tunnel (The Peering)

    Go to the VNet-Alpha page in the portal.

    In the left-hand menu, under Settings, click Peerings.

    Click + Add.

    This side (VNet-Alpha):

        Peering link name: Alpha-to-Beta

    Remote virtual network (VNet-Beta):

        Peering link name: Beta-to-Alpha

        Virtual network: Select VNet-Beta.

    Leave all other settings (Traffic to/from remote, Forwarded traffic) as Allow.

    Click Add.

    Note: Azure creates the connection in both directions automatically now. Wait for the status to say "Connected."

3. Verification (The "Ping" Test)

    SSH/Bastion into VM-Alpha.

    Try to "ping" or "curl" VM-Beta using its Private IP:
    Bash

    ping 10.2.0.4

    The Result: Even though they are in separate networks, the ping will work!

4. Why use this? (Real World Scenario)

In a professional setup, companies use a Hub-and-Spoke model:

    The Hub: Contains shared resources like a Firewall, Bastion, or a VPN Gateway.

    The Spokes: Different teams (Marketing, HR, Dev) have their own VNets.

    Peering: All Spokes peer to the Hub to share the security tools without needing to buy them 5 times.