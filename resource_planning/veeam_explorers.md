<!--- This was last Changed 03-05-17 by PS --->
# Veeam Explorers

Veeam Explorers are tools included in all editions for item-level recovery from several application. As of v9.5, following Explorers are available:

* Veeam Explorer for Active Directory
* Veeam Explorer for SQL Server
* Veeam Explorer for Exchange
* Veeam Explorer for SharePoint
* Veeam Explorer for Oracle
* Veeam Explorer for Storage Snapshots

Each Explorer has a corresponding user guide available in Helpcenter: [Veeam Backup Explorers User Guide](https://helpcenter.veeam.com/docs/backup/explorers/introduction.html?ver=95u4). For specifics of performing granular restore of certain applications refer to the Applications section of this guide.

## Explorer for Storage Snapshots
Veeam Explorer for Storage Snapshots (VESS) is included, but it is
related to storage integrations with primary storage. This is explained in
the [Backup from Storage Snapshots](./backup_from_storage_snapshots.md) section
of this guide.

VESS is a very easy way to perform item-level recovery directly from storage
snapshots. Veeam is able to use discover and mount any storage snapshot for
restores. By combining the Veeam application consistent with crash consistent
snapshots, the RPO for certain applications can be significantly reduced.

Explorer for Storage Snapshots detailed description is available in the vSphere user guide of Veeam Backup & Replication: [Veeam Backup Explorer for Storage Snapshots](https://helpcenter.veeam.com/archive/backup/95/vsphere/restore_storage_snapshots_hiw_hp.html).
