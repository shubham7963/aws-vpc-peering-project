# AWS VPC Peering — Configuration Details

## 1. Environment

- **Cloud provider:** Amazon Web Services (AWS)
- **Region:** Asia Pacific (Mumbai), `ap-south-1`
- **Project:** Private connectivity between two VPCs
- **Operating system:** Amazon Linux 2023 on EC2
- **Access method:** SSH through a bastion host

## 2. VPC and Subnet Configuration

| Resource | CIDR / Private IP |
|---|---|
| VPC A — `ewb-vpc-a` | `10.10.0.0/16` |
| VPC A public subnet — `ewb-a-public` | `10.10.1.0/24` |
| VPC A private subnet — `ewb-a-private` | `10.10.2.0/24` |
| VPC B — `ewb-vpc-b` | `10.20.0.0/16` |
| VPC B private subnet — `ewb-b-private` | `10.20.1.0/24` |

The two VPCs use non-overlapping CIDR ranges, allowing them to communicate through VPC Peering.

## 3. EC2 Instances

| Instance role | Private IP | Purpose |
|---|---|---|
| Bastion host | `10.10.1.132` | Entry point for SSH access |
| App-A | `10.10.2.10` | Private instance in VPC A |
| App-B | `10.20.1.10` | Private instance in VPC B |

The bastion host's public IP during the lab was `13.127.253.208`.

## 4. VPC Peering

- **Connection name:** `ewb-a-b-peer`
- **Connection ID:** `pcx-0e2c78caa6fdd1667`
- **Recorded status:** Active during testing

The peering connection provided a private network path between VPC A and VPC B. It did not automatically configure routes or security group permissions; those had to be configured separately.

## 5. Route Table Configuration

The required routes for inter-VPC communication were:

| Route table | Destination | Target |
|---|---|---|
| VPC A private route table | `10.10.0.0/16` | Local |
| VPC A private route table | `10.20.0.0/16` | VPC Peering |
| VPC A public route table | `10.20.0.0/16` | VPC Peering |
| VPC A public route table | `0.0.0.0/0` | Internet Gateway |
| VPC B private route table | `10.20.0.0/16` | Local |
| VPC B private route table | `10.10.0.0/16` | VPC Peering |

The private subnets must be associated with their intended route tables for the configured routes to take effect. The Internet Gateway route was used for the public subnet's internet access, not for traffic between the two VPCs.

## 6. Security Group Rules

The intended inbound rules for the private instances were:

### App-A security group

- SSH — TCP port 22 from bastion private IP `10.10.1.132/32`.
- ICMP Echo Request from App-B private IP `10.20.1.10/32`.

### App-B security group

- SSH — TCP port 22 from bastion private IP `10.10.1.132/32`.
- ICMP Echo Request from App-A private IP `10.10.2.10/32`.

Security groups are stateful, so responses to permitted connections are allowed automatically. The actual saved security group screenshots should be used to verify the final rules.

## 7. Connectivity Tests

The lab included the following tests:

1. Baseline connectivity testing before the final peering configuration.
2. Forward ping from App-A (`10.10.2.10`) to App-B (`10.20.1.10`).
3. Reverse ping from App-B to App-A.
4. SSH access to App-A through the bastion host.
5. SSH access to App-B through the bastion host.

Successful output should be confirmed against the corresponding screenshots in the `screenshots/` directory.

## 8. Cleanup

The project resources were intended to be removed after testing to help avoid ongoing charges. This repository preserves documentation and available evidence rather than a live AWS deployment.

## 9. Key Takeaways

- VPC Peering enables private routing between separate VPCs.
- Both sides need appropriate routes for bidirectional communication.
- Security groups must allow the required inbound traffic.
- A bastion host provides a controlled SSH entry point to private instances.
- Connectivity should be verified using actual command output and AWS configuration evidence.