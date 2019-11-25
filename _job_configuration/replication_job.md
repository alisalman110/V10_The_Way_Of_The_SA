# Replication Job

Veeam Backup & Replication supports a number of replication scenarios that depend on the location of the target host and will be discussed later in this section.

During replication cycles, Veeam Backup & Replication creates the following files for a VM replica:

-   A full VM replica (a set of VM configuration files and virtual disks).

During the first replication cycle, Veeam Backup & Replication copies these files to the selected datastore to the *&lt;ReplicaName&gt;* folder, and registers a VM replica on the target host.

-   Replica restore points (snapshot delta files). During incremental runs, the replication job creates a snapshot delta file in the same folder, next to a full VM replica.
-   Replica metadata where replica checksums are stored. Veeam Backup & Replication uses this file to quickly detect changed blocks of data between two replica states. Metadata files are stored on the backup repository (source side).

During the first run of a replication job, Veeam Backup & Replication creates a replica with empty virtual disks on the target datastore. Disks are then populated with data copied from the source side. 

To streamline the replication process, you can deploy the backup proxy on a virtual machine. The virtual backup proxy must be registered on an ESXi host with direct connection to the target datastore. In this case, the backup proxy will be able to use the Virtual Appliance (HotAdd) transport mode for writing replica data to target. In case of a NFS datastore at the target location, you can as well use Direct Storage access mode (Direct NFS) to write the data.

If the [Virtual Appliance](https://helpcenter.veeam.com/backup/vsphere/virtual_appliance.html?ver=95) mode is applicable, replica virtual disks are mounted to the backup proxy and populated through the ESX I/O stack. This results in increased writing speed and fail-safe replication to ESXi targets. For information on Virtual Appliance mode, see <https://helpcenter.veeam.com/backup/vsphere/virtual_appliance.html?ver=95>.

If the backup proxy is deployed on a physical server, or the Virtual Appliance or Direct NFS mode cannot be used for other reasons, Veeam Backup & Replication will use the [Network](https://helpcenter.veeam.com/backup/vsphere/network_mode.html?ver=95) transport mode to populate replica disk files. For information on the Network mode, see <https://helpcenter.veeam.com/backup/vsphere/network_mode.html?ver=95>.

The Direct SAN mode (as part of Direct Storage Access) can only be used together with replication targets in case of transferring thick-provisioned VM disks at the first replication run. As replication restore points are based on VMware snapshots, that are thin provisioned by definition, Veeam will failback to Virtual Appliance (HotAdd) mode or Network mode, if configured at proxy transport settings. Direct SAN mode or backup from storage snapshots can be used on the source side in any scenario.

**Note:** Veeam Backup and Replication supports replicating VMs residing on VVOLs but VVOLs are not supported as replication target datastore.

**Note:** Replication of encrypted VMs is supported but comes with requirements and limitations outlined in the [corresponding section](https://helpcenter.veeam.com/docs/backup/vsphere/encrypted_vms_backup.html?ver=95u4#replication) of the User Guide. Replication of encrypted VMs is NOT supported when the target is Veeam Cloud Connect.


### Offsite Replication


It is recommended to place a Veeam backup server on the replica target side so that it can perform a failover when the source side is down. When planning off-site replication, consider advanced possibilities — replica seeding, replica mapping and WAN acceleration. These mechanisms reduce the amount of replication traffic while network mapping and re-IP streamline replica configuration.

Plan for possible failover carefully. DNS and possibly authentication services (Active Directory, for example, or DHCP server if some replicated VMs do not use static addresses) should be implemented redundant across both sides. vCenter Server (and vCD) infrastructure should be as well considered for the failover scenario. In most cases, Veeam does not need a vCenter Server for replica target processing. It can be best practice to add the ESXi hosts from the replica target side (only) directly to Veeam Backup & Replication as managed servers and to perform replication without vCenter Server on the target side. In this scenario a failover can be performed from the Veeam console without a working vCenter Server itself (for example to failover the vCenter Server virtual machine).

Replication bandwidth estimation has always been a challenge, because it depends on multiple factors such as the number and size of VMs, change rate (at least daily, per RPO cycle is ideal), RPO target, replication window. Full information about these factors, however, is rarely at hand. You may try to set up a backup job having the same settings as the replication job and test the bandwidth (as the backup job will transfer the same amount of data as the replication job). **Veeam ONE** (specifically Infrastructure Assessment report packs) may help with estimating change rates and collecting other information about the infrastructure.

Also, when replicating VMs to a remote DR site, you can manage network traffic by applying traffic throttling rules or limiting the number of data transfer connections. See Veeam Backup & Replication User Guide for more information: <https://helpcenter.veeam.com/docs/backup/vsphere/setting_network_traffic_throttling.html?ver=95>.

 **Tip:** Replication can leverage WAN acceleration allowing a more effective use of the link between the source and remote sites. For more information, see the User Guide <https://helpcenter.veeam.com/docs/backup/vsphere/wan_acceleration.html?ver=95> or the present document (the [WAN Acceleration](../resource_planning/wan_acceleration.md) section above).


Also, when replicating VMs to a remote DR site, you can manage network traffic by applying traffic throttling rules or limiting the number of data transfer connections. See Veeam Backup & Replication User Guide for more information: <https://helpcenter.veeam.com/backup/vsphere/setting_network_traffic_throttling.html?ver=95>.


 **Tip:**  Replication can leverage WAN acceleration allowing a more effective use of the link between the source and remote sites. For more information, see the User Guide <https://helpcenter.veeam.com/docs/backup/vsphere/wan_acceleration.html?ver=95> or the present document (the “WAN Acceleration“ section above).

### Replication from Backups

When using replication from backup, the target VM is updated using data coming from the backup files created by a backup or backup copy job.

In some circumstances, you can get a better RTO with an RPO greater or equal to 24 hours, using replicas from backup. A common example beside the usage of proactive VM restores, is a remote office infrastructure, where the link between the remote site and the headquarters provides limited capacity.

In this case, the data communication link should be mostly used for the critical VM replicas synchronization with a challenging RPO. Now, assuming that a backup copy job runs for all VMs every night, some non-critical VMs can be replicated from the daily backup file. This requires only one VM snapshot and only one data transfer.

**Tip:** This feature is sometimes named and used as proactive restore. Together with SureReplica, it is a powerful feature for availability.

### Backup from Replica

It may appear an effective solution to create a VM backup from its offsite replica (for example, as a way to offload a production infrastructure); however, this design is not at all valid because of VMware limitations concerning CBT (you cannot use CBT if the VM was never started). There is a very well documented forum thread about this subject: <https://forums.veeam.com/vmware-vsphere-f24/backup-the-replicated-vms-t3703-90.html>.
