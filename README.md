# Bastion Lab — Secure AWS Network Architecture


## Table of contents

* Overview
* Architecture (what I built)
* Files & demo
* Screenshots (what each image shows)
* How to reproduce (high-level)
* Security notes
* Contact / License

## Overview

This project shows a secure network layout built on Amazon Web Services. It uses a VPC with public and private subnets, an Internet Gateway, a public Ubuntu bastion EC2, private EC2 servers, identity-based access via IAM + SSM, and centralized logging and monitoring.

## Architecture (what I built)

* **VPC** with public and private subnets to separate internet-facing systems from internal systems.
* **Internet Gateway (IGW)** attached to the VPC so public subnet resources can reach the internet.
* **Public subnet** hosts an **Ubuntu bastion host** (EC2). Admins connect here first instead of exposing private servers directly.
* **Private subnet** contains private EC2 servers and databases that are not internet-accessible.
* **Access control** via **AWS IAM** and **AWS Systems Manager (SSM)** so admins use identity-based access (no SSH keys floating around).
* **Monitoring & logging**: VPC Flow Logs, AWS CloudTrail, CloudWatch, and GuardDuty to detect suspicious behavior and keep an audit trail.

## Files & demo

* `README.md` — (this file)
* `video demo/` — video demo (watch this to see the lab in action)
* `screenshots/` — all screenshots referenced below (named by screenshot title)

## Screenshots — what each image shows

* **Bastion EC2**
  This screenshot shows the creation of the Ubuntu bastion host.

* **Bastion Flow Log**
  This screenshot shows the creation of the bastion flow logs.

* **Bastion IAM User**
  This screenshot shows the IAM user role for the SSM Bastion Access. It has access to SSM and a special role for SSM Bastion Access.

* **Bastion SSM Policy**
  This screenshot shows the policy for SSM Bastion access that is attached to the Bastion IAM user.

* **Bastion VPC**
  This screenshot shows the creation of the Bastion Lab VPC for this project.

* **CloudWatch**
  This screenshot shows the CloudWatch configuration. It is attached to the Flow Logs.

* **IG**
  This screenshot shows the creation of the Internet Gateway for the Bastion host.

* **Logs**
  This screenshot shows the log events for the VPC Flow Log.

* **Private EC2**
  This screenshot shows the configuration and creation of the private server.

* **Private Subnet**
  This screenshot shows the creation and configuration of the private subnet where the private EC2 instance runs.

* **Public Routing Table**
  This screenshot shows the public routing table that allows the Bastion host to access the internet.

* **Public Subnet**
  This screenshot shows the creation and configuration of the public subnet for the Bastion host.

* **EC2SSMROLE**
  This screenshot shows the IAM policies used when creating the EC2 SSM role.

> Tip: screenshots are in `screenshots/` and are named to match these headings so you can find them fast.

## How to reproduce (high-level steps)

1. Create a new VPC with at least two subnets: public and private.
2. Attach an Internet Gateway to the VPC and add a public route for the public subnet.
3. Launch an Ubuntu EC2 in the public subnet and enable SSM (attach an SSM-capable role).
4. Launch private EC2 instances in the private subnet with the EC2 SSM role so they can be managed from the bastion using SSM Session Manager.
5. Create IAM users/roles and a dedicated SSM Bastion Access policy that limits who can start SSM sessions and which instances they can access.
6. Enable VPC Flow Logs and send them to CloudWatch Logs; enable CloudTrail and GuardDuty for threat detection.
7. Test access: connect via SSM Session Manager to bastion, then start sessions to private instances (no direct SSH over the internet).
8. Verify logs appear in CloudWatch and CloudTrail and that GuardDuty shows no critical findings.

## Security notes (why this is safer)

* **Network segmentation** lowers the attack surface by keeping critical servers off the internet.
* **Bastion + SSM** removes reliance on long-lived SSH keys and centralizes access through IAM policies.
* **Least privilege**: admin access is limited by IAM and the SSM Bastion policy.
* **Visibility**: Flow Logs, CloudTrail, CloudWatch, and GuardDuty give audit trails and alerts so you can spot suspicious activity quickly.
* **Defense in depth**: network controls, identity controls, and monitoring work together so a single failure doesn’t expose everything.

## Quick checklist (what to verify)

* [ ] VPC has both public and private subnets
* [ ] IGW attached and public route table configured
* [ ] Bastion host has SSM agent enabled and correct IAM role
* [ ] Private instances have SSM role and no public IPs
* [ ] IAM policy for SSM Bastion access created and attached to correct users/groups
* [ ] VPC Flow Logs, CloudTrail, CloudWatch, and GuardDuty enabled and tested

