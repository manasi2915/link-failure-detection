# SDN Link Failure Detection and Recovery

## Problem Statement
In traditional networks, link failures require manual intervention or slow convergence protocols. This project implements an SDN-based solution using Mininet and Ryu controller to automatically detect link failures and dynamically reroute traffic through a backup path, restoring connectivity within seconds.

## Topology
- 4 OpenFlow switches (s1, s2, s3, s4) in a ring
- 4 hosts (h1, h2, h3, h4), one connected to each switch
- Primary path: h1 to s1 to s2 to s3 to s4 to h4
- Backup path: h1 to s1 to s4 to s3 to s2 to h4 (used when s1-s2 link fails)

## SDN Controller Logic
- Learning switch: learns MAC-to-port mappings dynamically
- Installs flow rules (match+action) for known destinations
- Handles PORT_STATUS events to detect link failures
- Clears stale flow rules on failure so traffic reroutes via backup path
- Re-learns paths when link recovers

## Requirements
- Ubuntu 22.04
- Mininet
- Ryu SDN Controller 4.34
- Python 3.10
- iperf3, Wireshark

## Setup and Installation

Install Mininet:
sudo apt update
sudo apt install mininet -y

Install Ryu:
python3 -m venv ~/ryu-env
source ~/ryu-env/bin/activate
pip install eventlet==0.33.3 dnspython==2.2.1 ryu

## Running the Project

Terminal 1 - Start Ryu Controller:
source ~/ryu-env/bin/activate
ryu-manager controller.py --ofp-tcp-listen-port 6633

Terminal 2 - Start Mininet Topology:
sudo python3 topology.py

## Test Scenarios

Scenario 1 - Normal Operation:
mininet> pingall
Expected: 0% packet loss (12/12 received)

Scenario 2 - Link Failure and Recovery:
mininet> h1 ping h4
mininet> link s1 s2 down
mininet> link s1 s2 up
Expected: ping shows unreachable during failure then automatically recovers via backup path

Scenario 3 - Throughput Test:
mininet> iperf h1 h4
Expected: ~20 Gbits/sec throughput

## Expected Output
- Controller logs: LINK FAILURE DETECTED switch=1 port=1
- Ping recovers automatically via backup path s1 to s4 to s3 to s2
- Flow tables update dynamically after failure

## References
1. Mininet overview - https://mininet.org/overview/
2. Ryu SDN Framework - https://ryu-sdn.org/
3. OpenFlow 1.3 Specification - https://opennetworking.org/
4. Mininet Walkthrough - https://mininet.org/walkthrough/

Name:Manasi Vipin
SRN:PES1UG24CS260
