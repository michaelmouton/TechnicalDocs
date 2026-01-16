# Creating a Proxmox cluster, and joining it

### Creating the cluster
On your first node, PVE-NODE-01, run the following commands to create the cluster, and check its status...

```
pvecm create PVE-PRIVATE-CLOUD
```

### Joining the cluster
Now on all your other nodes, run the following command to joing it to the same PVE-PRIVATE-CLOUD cluster...

```
pvecm add 192.168.202.111
```

The above command assumes 192.168.202.111 is the IP address of PVE-NODE-01.

More info can be found here...
https://pve.proxmox.com/wiki/Cluster_Manager
