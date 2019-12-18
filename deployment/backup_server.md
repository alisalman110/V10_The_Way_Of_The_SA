# Backup Server

Veeam Backup & Replication is a modular solution that lets you build a scalable availability infrastructure for environments of different sizes and configurations. The Backup Server is the core component. Features & component requirements will affect your decision how you install the backup server e.g. one datacenter or multiple locations. It could mean that you choose to install additional backup servers or services in remote locations to optimize the data streams.

In an enterprise environment, you may choose to install an additional backup server to speed up the recovery process during a disaster.

Before deployment and initial configuration of the backup server it is recommended to go through the assessment and requirements collection outlined in the [Preparation](../preparation/README.md) section of this guide.

## Requirements

The Veeam Backup server requires Microsoft Windows 2008 R2 or later (64-bit only). The latest supported version of Windows OS is always recommended (currently Microsoft Windows 2016) as it will also support restoring from virtual machines with ReFS file systems or workloads with Windows Server Deduplication enabled.

## Placement

You may deploy the Veeam Backup & Replication server as either a physical or virtual server. Install Veeam Backup & Replication and its components on dedicated machines. Backup infrastructure component roles can be co-installed. The following guidelines may help in deciding which deployment type is the best fit for your environment.

### Virtual Deployment

For most cases, virtual is the recommended deployment. As it provides high availability for the backup server component via features like vSphere High Availability, vSphere Fault Tolerance or Hyper-V Failover Clustering. It also provides great flexibility in sizing and scaling as the environment grows.

The VM can also be replicated to a secondary location such as a DR site. If the virtual machine itself should fail or in the event of a datacenter/infrastructure failure, the replicated VM can be powered on. Best practice in a two-site environment is to install the Backup server in the DR site, in the event of a disaster it is already available to start the recovery.

## Physical deployment

In small-medium environments (up to 500 VMs) it is common to see an all-in-one physical server running the Backup & Replication server, backup proxy and backup repository components. This is also referred to as an "Appliance Model" deployment.

In large environments (over 2,500 VMs) installing Backup & Replication services on separate servers either virtual or physical will provide better performance. When running many jobs simultaneously, _consuming large amounts of CPU and RAM,_ scaling up the virtual Backup & Replication server to satisfy the system requirements may become impractical.

An advantage of running the Veeam Backup & Replication server on a physical server is that it runs independently from the virtual platform. This might be an ideal situation when recovering the virtual platform from a disaster.

## Protecting Backup Server and Configuration

__Tip:__ It is recommended to store the configuration backup, _using a file copy job_, in a location that is always available to this standby Backup & Replication server.

### TBD

 Should the physical server itself fail, there are additional steps to take before reestablishing operations:

  1. Install and update the operating system on a new server
  2. Install Veeam Backup & Replication
  3. Restore the configuration backup

## Sizing

## See Also

* [User Guide - System Requirements](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95#backup_server)
