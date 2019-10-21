# VM Restore

## VM Data Transport

To write VM disk data to the target datastore, Veeam Backup &
Replication can use one of the 3 transport modes:

- Direct SAN Access
- Virtual Applicance (HotAdd)
- Network (NBD)

For more information about each transport mode, see [Veeam Backup &
Replication User
Guide](https://helpcenter.veeam.com/docs/backup/vsphere/transport_modes.html?ver=95)
and the corresponding sections of this document.

### Direct SAN Access Data Transport Mode

This mode is available only for VMs that have all disks in thick
provisioning.

In the Direct SAN Access mode, Veeam Backup & Replication connects to
the ESXi host where the restored VM is registered. The ESXi host locates
the VM disks, retrieves metadata about the disk layout on the storage,
and sends this metadata to the backup proxy. The backup proxy uses this
metadata to copy VM data blocks to the datastore via SAN.

### Virtual Appliance Data Transport Mode

In the Virtual Appliance transport mode, VM disks from the backup are
hot-added to a virtualized Veeam backup proxy. The proxy connects to the
ESXi host where the restored VM resides and transfers disk data to the
target datastore through the ESX(i) I/O stack. When the data transfer
process is finished, disks are unmapped from the backup proxy.

### Network Data Transport Mode

In the Network transport mode, Veeam backup proxy connects to the ESXi
host where the restored VM resides, and writes VM disk data to the
target datastore through the LAN channel.
