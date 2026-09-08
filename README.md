# 3-Tier Architecture on AWS

A hands-on implementation of a classic **3-tier architecture** on AWS — Web, Application, and Database tiers — built from scratch using a custom VPC, subnetting, routing, EC2, RDS, and Security Groups.

![AWS](https://img.shields.io/badge/AWS-VPC%20%7C%20EC2%20%7C%20RDS-orange?logo=amazonaws)
![Status](https://img.shields.io/badge/status-complete-brightgreen)

---

## 📐 Architecture Overview

```
                              Internet
                                 │
                          Internet Gateway
                                 │
        ┌────────────────────────────────────────────┐
        │                 VPC (10.0.0.0/16)           │
        │                                              │
        │   Public Subnets (Web Tier)                  │
        │   ┌───────────┐   ┌───────────┐             │
        │   │  web-1    │   │  web-2    │  AZ-a / AZ-b │
        │   └───────────┘   └───────────┘             │
        │        │  Public Route Table (→ IGW)         │
        │        ▼                                     │
        │   Private Subnets (App Tier)                 │
        │   ┌───────────┐   ┌───────────┐             │
        │   │  app-1    │   │  app-2    │  AZ-a / AZ-b │
        │   └───────────┘   └───────────┘             │
        │        │  Private Route Table (local only)   │
        │        ▼                                     │
        │   Private Subnets (Database Tier)             │
        │   ┌───────────┐   ┌───────────┐             │
        │   │ database-1│   │ database-2│  AZ-a / AZ-b │
        │   └───────────┘   └───────────┘             │
        │                                              │
        └────────────────────────────────────────────┘
```

## 🧱 What Was Built

| Layer | Component | Details |
|---|---|---|
| **Networking** | VPC | Custom VPC, `10.0.0.0/16` |
| | Subnets | 6 subnets (web / app / database), 2 per tier across 2 AZs |
| | Route Tables | Public RT → Internet Gateway; Private RT → local only |
| | NAT Gateway | Intentionally omitted to keep the build cost-minimal |
| **Compute** | EC2 | 4× `t3.micro` instances across the web & app tiers |
| | Security Groups | Custom SG scoping traffic between tiers |
| **Database** | RDS | MySQL instance deployed in the isolated database subnets |
| **Storage** | EBS | `gp3` volumes (8 GiB, 3000 IOPS) backing each instance |

## 🔐 Design Principles

- **Tier isolation** — only the web tier has a route to the Internet Gateway; app and database tiers use a route table with no `0.0.0.0/0` route.
- **High availability** — every tier is duplicated across two Availability Zones.
- **Least exposure** — the database tier is never directly reachable from the internet; only the app tier is permitted to reach it.
- **Defense in depth** — subnet-level routing plus Security Groups enforce the tier boundaries independently.

## 🛠️ Steps Followed

1. Created a custom VPC (`10.0.0.0/16`)
2. Created 6 subnets (2× web, 2× app, 2× database) across 2 AZs
3. Created and attached an Internet Gateway
4. Configured a public route table (→ IGW) and associated it with web subnets
5. Configured a private route table (local-only) and associated it with app/database subnets
6. Launched EC2 instances into the web/app tiers
7. Created a Security Group scoping inter-tier traffic
8. Provisioned an RDS (MySQL) instance in the database subnets
9. Verified EBS volumes backing each instance

## 📄 Documentation

See [`3-Tier-Architecture-AWS.pdf`](./3-Tier-Architecture-AWS.pdf) for the full walkthrough with console screenshots.

## 🚀 Possible Next Steps

- Add a NAT Gateway for outbound internet access from private subnets
- Front the web tier with an Application Load Balancer + Auto Scaling Group
- Add CloudWatch alarms and an RDS Multi-AZ standby
- Recreate this as Infrastructure as Code (Terraform / CloudFormation)

## 🏷️ Tags

`aws` `vpc` `3-tier-architecture` `ec2` `rds` `networking` `cloud-computing`
