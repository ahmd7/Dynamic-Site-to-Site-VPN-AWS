# Advanced Highly-Available Dynamic Site-to-Site VPN

This repository provides a CloudFormation template (`BGPVPNINFRA.yaml`) and detailed instructions to set up an advanced, highly-available dynamic Site-to-Site VPN environment on AWS.

## Shout-out to YouTube Video

A big shout-out to the YouTube video that guided me through this project! Check out the tutorial [here](https://youtu.be/0dVVLKp4I18?si=cEXYK_-jUGN6z95G).

## Stage 1A - Initial Setup of AWS Environment and Simulated On-Premises Environment

1. Apply `BGPVPNINFRA.yaml` to the `us-east-1` region in your AWS account using the CloudFormation console.

2. Wait for the stack to move into a `CREATE_COMPLETE` status.

## Stage 1B - Create Customer Gateway Objects

3. Open the VPC Console in a new tab.

4. Open the CloudFormation Console in a new tab.

5. In the CloudFormation Tab, click on the stack, then navigate to Outputs. Note down the IP addresses for `Router1Public` and `Router2Public`.

6. In the VPC Console, under Virtual Private Network (VPN), select `Customer Gateways`.

7. Click `Create Customer gateway`, set the name to `ONPREM-ROUTER1`, choose `Dynamic` for routing, set BGP ASN to `65016` (you can use any), and set IP Address to `Router1PubIP`. Click `Create Customer gateway`.

8. Repeat the process for `ONPREM-ROUTER2` with the corresponding IP Address.

## Stage 2A - Create VPN Attachments for Transit Gateway

9. Move to `Transit Gateway Attachments` and create attachments for `ONPREM-ROUTER1` and `ONPREM-ROUTER2` to the Transit Gateway (`A4LTGW`).

10. Move to `Site-to-Site VPN Connections` under Virtual Private Network, and download configurations for `Router1PubIP` and `Router2PubIP`. Rename the files to `CONNECTION1CONFIG.TXT` and `CONNECTION2CONFIG.TXT`.

## Stage 2B - Populate Demo Value Template

11. Populate the DemoValueTemplate using the provided instructions.

## Stage 3A - Configure IPsec Tunnels for ONPREMISES-ROUTER1

12. Before proceeding, wait for both VPN connections' state to change from `pending` to `available`.

13. Connect to `ONPREM-ROUTER1` using the EC2 Console and Session Manager.

14. Edit IPsec configuration files (`ipsec.conf`, `ipsec.secrets`, `ipsec-vti.sh`) with real values from `DemoValueTemplate.md`.

15. Copy files to `/etc` and restart StrongSwan with `systemctl restart strongswan`.

## Stage 3B - Configure IPsec Tunnels for ONPREMISES-ROUTER2

16. Connect to `ONPREM-ROUTER2` and repeat steps 14 and 15 using the values from `CONNECTION2CONFIG.TXT`.

## Stage 4A - Configure BGP Routing for ONPREMISES-ROUTER1 and Test

17. Connect to `ONPREM-ROUTER1`, run `ffrouting-install.sh`, and configure BGP routing.

18. Show routes via the UI and `vtysh`.

19. Connect to `ONPREM-SERVER1` and test connectivity with `EC2-A`.

## Stage 4B - Configure BGP Routing for ONPREMISES-ROUTER2 and Test

20. Connect to `ONPREM-ROUTER2`, run `ffrouting-install.sh`, and configure BGP routing.

21. Show routes via the UI and `vtysh`.

22. Connect to `ONPREM-SERVER2` and test connectivity with `EC2-B`.

## Stage 5 - Cleanup

23. Delete VPN connections, and customer gateways. Wait for connections to be removed.

24. Delete the stack.

**Note:** Ensure thorough understanding of the steps and review the provided configuration files before executing.
