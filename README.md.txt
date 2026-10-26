# AWS VPC Peering — Private Connectivity Between Two VPCs

A hands-on AWS networking project demonstrating private communication between two Amazon Virtual Private Clouds (VPCs) using VPC Peering, route tables, security groups, and Amazon EC2 instances.

**Cloud Provider:** Amazon Web Services (AWS)  
**Region:** Asia Pacific (Mumbai) — `ap-south-1`  
**Domain:** Cloud Networking  
**Project Status:** Completed

---


## Project Overview

The objective of this project was to establish private network connectivity between two separate VPCs using AWS VPC Peering and validate communication between EC2 instances located in their private subnets.

The project covers VPC configuration, subnet creation, peering setup, routing, security group rules, SSH access, connectivity testing, and resource cleanup.

## Network Architecture

![AWS VPC Peering Architecture](architecture/architecture-diagram.png)

The network consists of two VPCs with non-overlapping IPv4 CIDR ranges.

### VPC A

- **VPC CIDR:** `10.10.0.0/16`
- **Public subnet:** `10.10.1.0/24`
- **Private subnet:** `10.10.2.0/24`
- **Bastion host:** Provides SSH access to private instances.
- **App-A:** Private EC2 instance with IP `10.10.2.10`.

### VPC B

- **VPC CIDR:** `10.20.0.0/16`
- **Private subnet:** `10.20.1.0/24`
- **App-B:** Private EC2 instance with IP `10.20.1.10`.

The VPCs communicate through a VPC Peering connection. Communication requires appropriate route table entries and security group permissions.

## AWS Services and Technologies

| Service or Technology | Purpose |
|---|---|
| Amazon VPC | Creates isolated virtual networks |
| VPC Peering | Connects the two VPC networks |
| Subnets | Separates public and private resources |
| Route Tables | Directs traffic to the correct destination |
| Security Groups | Controls inbound and outbound traffic |
| Amazon EC2 | Hosts the bastion and application instances |
| SSH | Provides secure remote access |
| ICMP Ping | Tests network connectivity |
| Amazon Linux 2023 | Operating system for EC2 instances |

**AWS Region:** Asia Pacific (Mumbai), `ap-south-1`

## Network Configuration

### VPC and Subnet Addressing

| Resource | CIDR Block |
|---|---|
| VPC A | `10.10.0.0/16` |
| VPC A public subnet | `10.10.1.0/24` |
| VPC A private subnet | `10.10.2.0/24` |
| VPC B | `10.20.0.0/16` |
| VPC B private subnet | `10.20.1.0/24` |

### EC2 Instances

| Instance | Private IP | Purpose |
|---|---|---|
| Bastion host | `10.10.1.132` | SSH entry point |
| App-A | `10.10.2.10` | Private instance in VPC A |
| App-B | `10.20.1.10` | Private instance in VPC B |

### VPC Peering

- **Connection name:** `ewb-a-b-peer`
- **Connection ID:** `pcx-0e2c78caa6fdd1667`
- **Recorded status:** Active during testing.

### Routing Requirements

| Route table | Destination | Target |
|---|---|---|
| VPC A private | `10.20.0.0/16` | VPC Peering |
| VPC A public | `10.20.0.0/16` | VPC Peering |
| VPC B private | `10.10.0.0/16` | VPC Peering |

Each VPC also retains its local route. The public subnet's default internet route, where configured, uses the Internet Gateway.

### Security Group Requirements

The intended rules allowed:

- SSH (TCP port 22) to private instances from the bastion host's private IP `10.10.1.132/32`.
- ICMP Echo Request to App-A from App-B's private IP `10.20.1.10/32`.
- ICMP Echo Request to App-B from App-A's private IP `10.10.2.10/32`.

Refer to the saved security group screenshots and configuration report for the recorded settings.

## Implementation Workflow

1. Created two VPCs with non-overlapping CIDR ranges.
2. Created public and private subnets.
3. Launched the bastion host, App-A, and App-B EC2 instances.
4. Established VPC Peering between the two VPCs.
5. Configured routes for inter-VPC traffic in both directions.
6. Configured security groups for SSH and ICMP connectivity.
7. Tested private instance access and network connectivity.
8. Captured screenshots of the configuration and test results.
9. Performed lab resource cleanup after testing.

## Traffic Flow

### Forward Traffic: App-A to App-B

`App-A → VPC A private route table → VPC Peering → VPC B private route table → App-B`

### Return Traffic: App-B to App-A

`App-B → VPC B private route table → VPC Peering → VPC A private route table → App-A`

Successful communication depends on the routes in both VPCs and the applicable security group rules. VPC Peering does not automatically create the required routes.

## Testing and Validation

The project included the following checks:

- VPC Peering connection status.
- Route table configuration.
- Security group configuration.
- SSH access to App-A through the bastion host.
- SSH access to App-B through the bastion host.
- Forward ping from App-A to App-B.
- Reverse ping from App-B to App-A.

Screenshots of available test results are provided in the `screenshots/` directory. Each result should be considered verified only when supported by the corresponding evidence.

## Repository Structure

```text
aws-vpc-peering-project/
├── README.md
├── .gitignore
├── SECURITY.md
├── architecture/
│   └── architecture-diagram.png
├── screenshots/
│   └── AWS configuration and testing evidence
└── docs/
    ├── Shubham_Badwane_EWB_VPC_Peering.pdf
    └── configuration-details.md
```

## Documentation

- [Project Report](docs/Shubham_Badwane_EWB_VPC_Peering.pdf)
- [Configuration Details](docs/configuration-details.md)
- [Network Architecture Diagram](architecture/architecture-diagram.png)

## Key Learnings

- Creating and configuring VPCs and subnets.
- Establishing private connectivity using VPC Peering.
- Configuring routes for forward and return traffic.
- Applying security group rules for SSH and ICMP.
- Accessing private instances through a bastion host.
- Troubleshooting connectivity between private EC2 instances.
- Cleaning up AWS resources after completing a lab.

## Resource Cleanup

The AWS lab resources were removed after testing to help avoid ongoing charges. This repository preserves project documentation and available evidence; it does not represent a currently running AWS deployment.

## Author

**Shubham Badwane**  
B.Tech — Computer Science and Engineering

---

