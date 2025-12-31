# Security Onion 2.4.90-2 Lab Setup

This directory contains the complete lab writeup and documentation for deploying Security Onion on Proxmox.

## Contents

- **SecurityOnion_Lab_Writeup.md** - Complete step-by-step lab documentation
- **images/** - Directory for screenshots and diagrams

## Quick Start

1. Read `SecurityOnion_Lab_Writeup.md` for complete setup instructions
2. Follow the step-by-step guide - **the writeup works without images!**
   - Detailed text descriptions and visual guides replace image references
   - Only one image (`so_alerts.png`) is valid; others are optional
3. (Optional) Replace corrupted images in `images/` directory if you have original screenshots

## What's Included

- Complete hardware requirements
- Network architecture documentation
- Proxmox VM configuration steps
- Network setup (bridges, promiscuous mode, offloading)
- Security Onion installation guide
- Comprehensive troubleshooting section
- Verification and testing procedures
- Maintenance and operations guide
- Complete configuration file examples

## Prerequisites

- Proxmox VE 8.x installed
- Managed switch with port mirroring capability
- Security Onion 2.4.90-2 ISO
- Minimum 16GB RAM for VM
- Two network interfaces on Proxmox host

## Key Features Documented

- Port mirroring configuration
- Proxmox bridge setup (Hub Mode)
- Checksum offloading fixes
- Interface configuration
- Alert generation testing
- Log rotation and maintenance

## Image Status

Most images in the `images/` directory are corrupted placeholders. The lab writeup has been updated with detailed text descriptions and visual guides, so **images are not required** to follow the instructions. See `images/README.md` for details.

## Support

Refer to the troubleshooting section in the writeup for common issues and solutions.

