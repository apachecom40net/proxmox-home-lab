# proxmox-home-lab


## Creating just the Management Interface
```
## ########### ########### #################### ########### ###########
## ip -br link show
## ########### ########### #################### ########### ###########


## Output of `ip -br link show` command

## lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP> 
## enp87s0          UP             58:47:ca:77:dc:10 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## enp89s0          UP             58:47:ca:77:dc:11 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## enp2s0f0np0      UP             58:47:ca:77:dc:0e <BROADCAST,MULTICAST,UP,LOWER_UP> 
## enp2s0f1np1      UP             58:47:ca:77:dc:0f <BROADCAST,MULTICAST,UP,LOWER_UP> 
## vmbr1            UP             58:47:ca:77:dc:11 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## vmbr0            UP             58:47:ca:77:dc:10 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## vmbr0.100@vmbr0  UP             58:47:ca:77:dc:10 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## wlp90s0          DOWN           94:8c:d7:c0:c5:3c <BROADCAST,MULTICAST> 

auto lo
iface lo inet loopback
    comment "Local loopabck device"

auto enp87s0
iface enp87s0 inet static
    address 192.168.70.100/24
    gateway 192.168.70.1
    comment "Primary network interface, Management network"

## Include files from /etc/network/interfaces.d:
source /etc/network/interfaces.d/*

## ########### ########### #################### ########### ###########
## Next Steps Build the 
## ########### ########### #################### ########### ###########
```

## ip -br link show execution after first update
<img width="791" height="185" alt="image" src="https://github.com/user-attachments/assets/e98d0e50-dde7-4659-a6ba-e109ecdd92cc" />




## Adding the Trunk
````
## ########### ########### #################### ########### ###########
## ip -br link show
## ########### ########### #################### ########### ###########


## Output of `ip -br link show` command

## lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP> 
## enp87s0          UP             58:47:ca:77:dc:10 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## enp89s0          UP             58:47:ca:77:dc:11 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## enp2s0f0np0      UP             58:47:ca:77:dc:0e <BROADCAST,MULTICAST,UP,LOWER_UP> 
## enp2s0f1np1      UP             58:47:ca:77:dc:0f <BROADCAST,MULTICAST,UP,LOWER_UP> 
## vmbr1            UP             58:47:ca:77:dc:11 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## vmbr0            UP             58:47:ca:77:dc:10 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## vmbr0.100@vmbr0  UP             58:47:ca:77:dc:10 <BROADCAST,MULTICAST,UP,LOWER_UP> 
## wlp90s0          DOWN           94:8c:d7:c0:c5:3c <BROADCAST,MULTICAST> 

auto lo
iface lo inet loopback
    comment "Local loopabck device"

auto enp87s0
iface enp87s0 inet static
    address 192.168.70.100/24
    gateway 192.168.70.1
    comment "Primary network interface, Management network"

# ---------------------------------------------------------
# 2️⃣ SFP+ TRUNK (LACP BOND)
# ---------------------------------------------------------
# Aggregates both 10GbE SFP+ ports for high availability and throughput
# VLAN tags (100/101/102) will be carried through this bond

auto bond0
iface bond0 inet manual
    bond-slaves enp2s0f0np0 enp2s0f1np1
    bond-miimon 100
    bond-mode 802.3ad
    bond-xmit-hash-policy layer3+4
    comment "LACP bond of SFP+ ports — VLAN trunk"

## ########### ########### #################### ########### ###########
## Next Steps Build the 
## ########### ########### #################### ########### ###########

## Include files from /etc/network/interfaces.d:
source /etc/network/interfaces.d/*
````

## After Update Trunk
<img width="840" height="157" alt="image" src="https://github.com/user-attachments/assets/e30c973b-4be5-4336-a377-64b84c508ebc" />

<br />
>
> From this point forward, we can choose between two approaches: defining VLANs directly in the /etc/network/interfaces file or managing them through Proxmox SDN. The recommended approach is outlined below.

