# Deduplication and Compression

## Storage Optimization Overview

Veeam Backup & Replication takes advantage of multiple techniques for optimizing the size of stored backups, primarily compression and deduplication. The main goal of these techniques is to strike the correct balance between the amount of data read and transferred during backup as well as what is stored on the backup target while providing acceptable backup and restore performance. Veeam Backup & Replication attempts to use reasonable defaults based on various factors but there can be cases when leveraging settings other than default might be valuable.

## Deduplication

### Best practices

  * Unless you have a really good understanding of the impact that can cause block size changing, stick to the defaults.
  * If you want to change the default block size, be sure to test it well and make sure you have planned appropriately for the extra I/O and memory requirements on the repository.
  * When using a block size smaller than the default one for a large server, it is recommended to use a backup mode that does not perform synthetic processing (like forward incremental with scheduled active full).

| Setting        | Block Size | Maximum recommended job size |
|----------------|------------|------------------------------|
| WAN            | 256 KB     | 4 TB of source data          |
| LAN            | 512 KB     | 8 TB of source data          |
| Local          | 1,024 KB   | 16 TB of source data         |
| Local (>16 TB) | 4,096 KB   | 64 TB of source data         |

### When do I change the defaults?

As a rule, the default settings provided by Veeam are designed to provide a good balance between backup size and backup and restore performance and resource usage during the backup process. However, given an abundance of processing resources or other specifics of the environment, it might be useful to change the defaults for a particular job.

**Note:** Block size changes will only become effective after an active full is created.

**Note:** Local (>16TB) underlying block size has changed in v9.0 from 8 MB to 4 MB. If you upgrade to Veeam Backup & Replication v9.0 from a previous version, this option will be displayed as "Local Target (legacy 8MB block size)" in the list and will still use the 8 MB blocks size. It is recommended that you switch to an option that uses a smaller block size and create an active full backup to apply the new setting. 

**Note** Large compressed or deduplicated source VMs – when backing up VMs, especially large VMs (>1 TB) that contain already compressed data (images, video, Windows deduplicated file servers, etc), it may be beneficial to simply disable Veeam deduplication since it is unlikely to gain additional space savings for this type of source data. Note that Veeam deduplication is a job-level setting so VMs of the same type should be grouped and processed within the same job.

## Compression

Veeam compression should almost always be enabled. However, when using a deduplicating storage system as a repository for storing Veeam backups, it might be desirable to disable Veeam compression at the repository level by using the **Decompress backup data blocks before storing** advanced option in repository configuration.

Enabling compression at the job level, and decompressing once sent to the repository will reduce the traffic between proxy server and backup repository by approximately 50% on average. If proxy and repository runs on the same server, the compression engine is automatically bypassed to prevent spending CPU for applying compression. The uncompressed traffic is sent between local data movers using shared memory instead.

### When do I change the defaults?

As a rule, the default settings provided by Veeam are designed to provide a good balance between backup size and backup and restore performance and resource usage during the backup process. However, given an abundance of resources or other specifics of the environment, it might be useful to change the defaults in particular circumstances. For example, if you know that CPU resources are plentiful, and backups are unable to make full use of the CPU due to other bottlenecks (disk/network), it might be worth increasing the compression level.

Compression settings can be changed on the job at any time and any new backup sessions will write new blocks with the new compression mode. Old blocks already stored in backups will remain in their existing compression level.

### Best Practices

-   Defaults are good, don’t change values without understanding the impact.
-   Use compression levels above optimal only if you have plenty of CPU and understand that maximum throughput, especially during full backups, will likely be significantly lower, especially if the backup proxy CPUs can’t take more load.
-   Test various compression levels and see how they impact the environment, but always remember the balance. A single backup job with a few concurrent streams may seem fine with **Extreme** compression, but may overload all available proxy CPUs during production run of all jobs.
-   Remember that higher compression ratios may also negatively impact restore speeds.

## BitLooker

The option "Exclude deleted file blocks" is the third configurable option in job settings. In several places you will see references to this feature under the name "BitLooker".

When enabled, the proxy server will perform inline analysis of the Master File Table (MFT) of NTFS file systems and automatically skip blocks that have been marked as deleted.

When upgrading from versions prior to v9.0, this setting is disabled for existing backup jobs. To enable it for existing jobs, use the following PowerShell commands.
Add-PSSnapIn VeeamPSSnapin;

```
Foreach ($job in Get-VBRJob) {
  $job.Options.ViSourceOptions.DirtyBlocksNullingEnabled = $true;
  $job.SetOptions($job.Options)
}
```

It is always recommended to leave BitLooker enabled, as it will reduce the amount of backup storage space required.
