# AWS VPC Peering Project Report

**Student:** Aadi Dhankhar  
**Institution:** Panipat Institute of Engineering and Technology  
**Project:** EWB Cloud Labs — AWS VPC Peering  
**AWS Region:** Asia Pacific (Sydney) — `ap-southeast-2`  
**GitHub repository:** Not created yet  
**Cleanup status:** Reported completed by student

> **Evidence note:** This report uses details visible in the supplied AWS Console screenshots and statuses reported by the student. Missing values and unverified results are marked honestly. Screenshots may be submitted separately.

## 1. Project Overview

The objective is to connect two Amazon Virtual Private Clouds (VPCs) privately using VPC peering and test communication between private EC2 instances. A bastion host in VPC A is intended to provide controlled SSH access to private instances.

## 2. VPC and Subnet Inventory

| Resource | ID / CIDR / details visible in screenshots |
|---|---|
| VPC A — `ewb-vpc-a` | `vpc-06aba3b871b11a606`; `10.10.0.0/16` |
| VPC B — `ewb-vpc-b` | `vpc-0a571318648a2aa4c`; `10.20.0.0/16` |
| Private subnet A — `ewb-subnet-a-private` | `subnet-0bdb323439f869b12`; `10.10.2.0/24`; auto-assign public IPv4: No |
| Private subnet B — `ewb-subnet-b-private` | `subnet-072e042f13a963847`; `10.20.1.0/24`; auto-assign public IPv4: No |
| App-A — `ewb-app-a` | `i-069b1e538e9a46c35`; private IP `10.10.2.40`; no public IPv4 shown |
| App-B — `ewb-app-b` | `i-0096b6e678fc68592`; private IP not visible in the supplied screenshot |
| Bastion — `ewb-bastion` | `i-0df2bbe39386cedbe`; public/private IPv4 not visible in the supplied screenshot |

## 3. VPC Peering and Route Tables

The supplied peering screenshot shows an **Active** peering connection between VPC A and VPC B in the Sydney region.

| Item | Screenshot value |
|---|---|
| Peering name | `ewb-a-b-peer` |
| Peering ID | `pcx-073ed0a20b797fdd7` |
| Requester VPC | `vpc-06aba3b871b11a606` / `ewb-vpc-a` |
| Requester CIDR | `10.10.0.0/16` |
| Accepter VPC | `vpc-0a571318648a2aa4c` / `ewb-vpc-b` |
| Accepter CIDR | `10.20.0.0/16` |
| Peering status | **Active** |
| Public route table — `ewb-rt-a-public` | `rtb-04c912d94f29bcba8` |
| Public route entries | `0.0.0.0/0` → `igw-094347b1eb7921952`; `10.10.0.0/16` → `local`; `10.20.0.0/16` → `pcx-073ed0a20b797fdd7` |
| Private route table A — `ewb-rt-a-private` | `rtb-05442b21b914ff2a8` |
| Private route table B — `ewb-rt-b-private` | `rtb-0f98b7401f67d9bc3` |
| Private route table B entries | `10.10.0.0/16` → `pcx-073ed0a20b797fdd7`; `10.20.0.0/16` → `local` |

**Verification needed:** The screenshot of private route table A's routes was not supplied. Confirm it includes `10.20.0.0/16` → `pcx-073ed0a20b797fdd7` before claiming both private route tables have been fully verified.

## 4. Security Groups

| Resource | Inbound rule shown in screenshots |
|---|---|
| `ewb-app-a` security group | ICMP (All), source `10.20.1.168/32`; TCP 22, source `10.10.1.15/32` |
| `ewb-app-b` security group | ICMP (All), source `10.10.2.40/32`; TCP 22, source `10.10.1.15/32` |
| `ewb-bastion` security group | TCP 22, source `157.49.147.82/32` |

- Confirm that `10.20.1.168` is App-B's actual private IP.
- Verify that `10.10.1.15` is the bastion's actual private IP.
- The bastion's SSH rule is restricted to `157.49.147.82/32`. If your public IP changes, this rule may need updating.
- Never upload private keys, passwords, AWS access keys, or tokens to GitHub.

## 5. Test Results

The student reported failed ping tests and connectivity errors from both the laptop terminal and while connecting to instances. The exact terminal error text was not provided.

| Test | Result |
|---|---|
| VPC peering | **Active** — shown in screenshot |
| Public route to VPC B | Peering route shown as active |
| Private route table B return route | Peering route shown as active |
| Ping connectivity | **Failed** — student-reported |
| Laptop → bastion SSH | Connectivity error reported; exact message unknown |
| Bastion → private instance SSH | Connectivity error reported; exact message unknown |
| Cleanup | **Completed** — student-reported |

No successful ping or SSH connection is claimed. The screenshots demonstrate selected configuration details, not successful end-to-end connectivity.

## 6. Bastion Troubleshooting

The exact cause cannot be confirmed without the terminal error message.

| Error type | Checks to perform |
|---|---|
| Connection timed out | Confirm the bastion public IPv4 address, Internet Gateway attachment, public route `0.0.0.0/0` → IGW, inbound TCP 22 from your current public IP `/32`, network ACLs, and EC2 status checks. |
| `Permission denied (publickey)` | Confirm the matching private key, correct Linux username, key-pair association, and local key-file permissions. Never share the private key. |
| No route / unreachable | Check subnet-to-route-table association, peering routes, security groups, network ACLs, and return routes. |
| Bastion works, private host fails | Connect to the target's private IP; confirm routes, target inbound TCP 22 from the bastion's actual private IP, SSH service, and host firewall. |
| Ping fails | Check ICMP inbound rules on both application security groups, actual peer private IPs, routes in both directions, and host firewall. |

**Specific checks suggested by the screenshots:** Verify whether the bastion's private IP is `10.10.1.15` and whether App-B's private IP is `10.20.1.168`. These values appear as security-group rule sources, but the supplied screenshots do not confirm the instance addresses themselves.

## 7. Conclusion

The screenshots show two VPCs with non-overlapping CIDR ranges, an active VPC peering connection, selected route-table entries, and security-group rules. However, ping tests failed and connectivity errors occurred during bastion/instance access. Successful end-to-end connectivity has therefore **not** been demonstrated.

## 8. Learning Reflection

This project helped me understand how VPC peering, route tables, security groups, and bastion access work together. Although the peering connection is active, connectivity tests failed. I need to verify the actual instance addresses and check that route tables and security rules match the intended traffic path.

## 9. Cleanup and GitHub Submission

Cleanup was reported as completed by the student; it has not been independently verified. Keep any cleanup evidence requested by your instructor.

Create a GitHub repository such as `AWS-VPC-Peering-Project` and upload:
- This Markdown report (`Aadi_Dhankhar_EWB_VPC_Peering.md`)
- A `README.md` explaining the project and folder contents
- Evidence screenshots, submitted separately if preferred by the instructor
- An architecture diagram, if available

Add the real GitHub repository URL after the repository is created. GitHub Pages can host project documentation, but it does not host the AWS VPC network itself.

**Suggested PDF filename if you later export this Markdown:** `Aadi_Dhankhar_EWB_VPC_Peering.pdf`

---

**Source basis:** EWB Cloud Labs Student Project Workbook and AWS Console screenshots supplied by the student. Values are marked as screenshot evidence or student-reported where applicable.
