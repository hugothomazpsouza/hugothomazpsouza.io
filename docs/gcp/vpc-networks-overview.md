# GCP VPC Networks - Complete Study Guide

> 📅 **Date:** 2026-02-24 | ✍️ **Author:** Hugo Souza | 🏷️ **Version:** 1.0.0

[![Status](https://img.shields.io/badge/Status-Study_Guide-blue?style=flat-square)](#)

## 🎯 Objective

This guide compiles the most important concepts regarding Virtual Private Cloud (VPC) Networks in Google Cloud Platform. It serves as a rapid review material for the **Professional Cloud Network Engineer** certification.

---

## 🏗️ 1. Core Concepts & Properties

A Virtual Private Cloud (VPC) network is a virtual version of a physical network, implemented inside Google's production network via **Andromeda**.

### Key Characteristics:

- **Global Scope:** VPC networks, routes, and firewall rules are global resources. They are not tied to a specific region or zone.
- **Regional Subnets:** Subnets (or subnetworks) are regional resources. A subnet defines a range of IP addresses (IPv4 and optionally IPv6).
- **Traffic Control:** Traffic is controlled by network firewall rules implemented natively on the VMs. Traffic is assessed as it leaves or arrives at the VM.
- **Internal Communication:** Resources inside a VPC can communicate via internal IPv4/IPv6 addresses, respecting firewall rules. They can also use Private Google Access to reach Google APIs using only internal IPs.
- **Default Network:** New projects automatically start with a `default` auto-mode VPC network (unless prohibited by Organization Policy `compute.skipDefaultNetworkCreation`).

---

## 🌐 2. Subnet Creation Modes

Google Cloud offers two main types of VPC networks:

### Auto Mode VPC Networks

- Automatically creates one subnet per Google Cloud region.
- Uses predefined IPv4 ranges fitting inside the `10.128.0.0/9` CIDR block.
- As new GCP regions launch, new subnets are added automatically.
- **Limitations:** Cannot overlap with your on-prem IP blocks if you use `10.128.0.0/9`. Cannot be peered with other Auto Mode networks. Does not support IPv6 ranges.
- **Conversion:** Can be converted to Custom Mode (one-way operation).

### Custom Mode VPC Networks

- **Recommended for Production.**
- No subnets are created automatically. Complete control over IP spaces and regions.
- Highly flexible, allows IP planning to prevent overlapping ranges with on-prem networks connected via Interconnect/VPN.
- Supports IPv6 subnets (Internal and External).
- Cannot be converted back to Auto Mode.

---

## 🛡️ 3. Routes & Firewall Rules

### Routes

- **Egress Paths:** Routes define paths for packets _leaving_ instance (egress).
- **Dynamic Routing Mode:** Controls the behavior of Cloud Routers in the VPC (determines how BGP routes are propagated - Regional or Global).
- **Internet Access:** For a VM to reach the internet, it needs:
  1. A valid default route to the internet gateway (`0.0.0.0/0` or `::/0`).
  2. Firewall rules allowing egress.
  3. An external IP address, OR access to Cloud NAT / instance-based proxy.

### Firewall Rules

- Can be standard **VPC Firewall Rules** or **Hierarchical Firewall Policies**.
- Controls traffic between VMs even if they are in the same VPC network.
- **Default VPC Rules:** Implied allow for egress, implied deny for ingress. Auto-mode default network comes with `default-allow-internal`, SSH, RDP, and ICMP rules.

---

## 🔗 4. Connectivity, Protocols & IAM

### Connectivity

- **IAM Security:** Network administration is secured via Identity and Access Management (Network Admin, Security Admin).
- **Shared VPC:** Keeps a VPC network in a common Host Project, allowing Service Projects from the organization to attach resources into the centralized subnets.
- **VPC Network Peering:** Connects VPC networks across different projects or organizations.
- **Hybrid Cloud:** Connect to on-prem via Cloud VPN or Cloud Interconnect.

### Supported Protocols

- **Internal VM-to-VM:** All IPv4 protocols.
- **VM to Internet:** ICMP, IPIP, TCP, UDP, GRE, ESP, AH, and SCTP.
- **Protocols & GRE limitations:** Cloud NAT does _not_ support GRE. Application/Proxy Load Balancers do _not_ support GRE. Passthrough Network Load Balancers support GRE using `L3_DEFAULT`.

---

## 🚀 5. Network Performance Metrics

For high-performance networking (like the Professional PCNE exam might query), remember these baselines achieved using `c2-standard` instances:

- **Intra-zone Latency:** Typically < 55 μs (50th percentile) to < 80 μs (99th percentile).
- **Compact Placement Policy:** Lowers intra-zone latency to < 45 μs (P50) to < 60 μs (P99).
- **Packet Loss Target:** Global average targeted below **0.01%** for cross-region traffic.
- **Traceroute Behavior:** Google Cloud increases the TTL counter internally, often hiding hops inside Google's network. Missing hops in `mtr`/`traceroute` are normal and do not equate to actual packet loss if the destination responds.

---

## 📚 Official References

- [🔗 Google Cloud VPC Documentation](https://cloud.google.com/vpc/docs/vpc)
- [🔗 Professional Cloud Network Engineer Exam Guide](https://cloud.google.com/certification/guides/cloud-network-engineer)

[⬅️ Back to GCP Index](README.md)
