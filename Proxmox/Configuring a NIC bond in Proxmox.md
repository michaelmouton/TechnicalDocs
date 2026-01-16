# Configuring a NIC bond in Proxmox

Assuming a fresh installation of Proxmox, you should ideally create an active-backup NIC bond, which makes use of two NICs.

In this example we'll be using eth5 and eth6, both of which are pinned network interface names.  By default eth5 is the active port, and eth6 is not running.

The layout should look like this...

```
eth5  ─┐
       ├─ bond0 (mode active-backup)
eth6  ─┘
           └─ vmbr0 (IP address lives here)
```

Prerequisites...

Before we start make sure you have full IPMI/BMC access to each server.  Should you lose connection, this will be your backup.

Start by getting a copy of ```/etc/network/interfaces```...

```
auto lo
iface lo inet loopback

iface nic5 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.202.111/24
        gateway 192.168.202.1
        bridge-ports nic5
        bridge-stp off
        bridge-fd 0

iface nic8 inet manual

iface nic0 inet manual

iface nic1 inet manual

iface nic2 inet manual

iface nic3 inet manual

iface nic4 inet manual

iface nic6 inet manual

iface nic7 inet manual


source /etc/network/interfaces.d/*
```

To create the bond and link the default bridge you need to do the following...

1. Configure ```eth5``` and ```eth6``` to autostart
2. Create ```bond0``` interface
3. Change the bridge port from ```eth5``` to ```bond0``` in the ```vmbr0``` interface

The final result should look like this...

```
auto lo
iface lo inet loopback

auto nic5
iface nic5 inet manual

auto nic6
iface nic6 inet manual

auto bond0
iface bond0 inet manual
        bond-slaves nic5 nic6
        bond-miimon 100
        bond-mode active-backup
        bond-primary nic5

auto vmbr0
iface vmbr0 inet static
        address 192.168.202.111/24
        gateway 192.168.202.1
        bridge-ports bond0
        bridge-stp off
        bridge-fd 0

iface nic0 inet manual

iface nic1 inet manual

iface nic2 inet manual

iface nic3 inet manual

iface nic4 inet manual

iface nic7 inet manual

iface nic8 inet manual

source /etc/network/interfaces.d/*
```

After configuring, run the following command to apply the configuration...
```
ifreload -a
```

If, for some reason, it does not apply the configuration, you should reboot...
```
reboot
```

### What to do port-configuration...

Verify the bond is correctly configured and running...
```
cat /proc/net/bonding/bond0
```

Also confirm the default bridge using ```bond0``` as its slave.
```
bridge link
```

### Adding dedicated VLAN interfaces

Ideally you should tag VLANs in the network tab of each VM.  Doing this means the interfaces file needs to be modified for every node in a cluster for migrating VMs.

```
auto lo
iface lo inet loopback

auto nic5
iface nic5 inet manual

auto nic6
iface nic6 inet manual

auto bond0
iface bond0 inet manual
        bond-slaves nic5 nic6
        bond-miimon 100
        bond-mode active-backup
        bond-primary nic5

auto bond0.3001
iface bond0.3001 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.202.111/24
        gateway 192.168.202.1
        bridge-ports bond0
        bridge-stp off
        bridge-fd 0

auto vmbr3001
iface vmbr3001 inet static
        bridge-ports bond0.3001
        bridge-stp off
        bridge-fd 0

iface nic0 inet manual

iface nic1 inet manual

iface nic2 inet manual

iface nic3 inet manual

iface nic4 inet manual

iface nic7 inet manual

iface nic8 inet manual

source /etc/network/interfaces.d/*
```

More info can be found [here](https://pve.proxmox.com/wiki/Network_Configuration).
