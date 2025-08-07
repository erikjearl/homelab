# Homelab Overview

A lightweight, two-node Kubernetes cluster with optional Raspberry Pi expansion.

## Nodes & Hardware

* **Control Node** `yellow` (OptiPlex 3050 SFF)

  * CPU: Intel i7-9700, 8 cores @ 3.0–4.8 GHz
  * RAM: 16 GB DDR4
  * Primary Storage: 256 GB NVMe SSD
  * Secondary Storage: 2 TB HDD
  * GPU: NVIDIA GT 730 (2 GB)
  * IP: 192.168.86.11

* **Worker Node** `blue` (OptiPlex 3050 MT)

  * CPU: Intel i5-7500, 4 cores @ 3.4 GHz
  * RAM: 8 GB DDR4
  * Storage: 500 GB SATA HDD
  * Wi-Fi: Intel 8265 802.11ac
  * IP: 192.168.86.12

* **Additional/Temp Nodes**

  * `pi4`: Raspberry Pi 4 (4 GB RAM) – IP 192.168.86.13
  * `pi2`: Raspberry Pi 4 (2 GB RAM) – IP 192.168.86.14

