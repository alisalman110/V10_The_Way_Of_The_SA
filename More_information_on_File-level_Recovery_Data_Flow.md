# **More information on File-level Recovery Data Flow**



![Data flow at restore](./pictures/backup_server_data_flow_2.png)

When file-level recovery is performed from the Veeam backup console, two mounts are initiated:

1. The remote console - for displaying restore point contents
2. The mount server - for performing actual restore traffic to the target VM

**Note:**  For VMs not running a Windows operating system, a Linux based FLR helper appliance mounts the backup file for reading the file system.

Between 50-400 MB of data is transferred between the console and backup repository. If the first file mount is performed over a slow connection it may take considerable time to load the file-level recovery wizard. If there is significant latency between the backup repository and console, it is recommended to deploy an instance of the console on or closer to the repository server.

### Veeam Enterprise Manager
Veeam Enterprise Manager is a self-service portal where administrators or service desk representatives can initiate restores for VMs, files, e-mail items, Oracle and SQL databases.

It is possible to avoid the first mount entirely by using "guest file system indexing"[^3]. When guest file system indexing is enabled, the content of the guest VM is stored in the Veeam Catalog and presented through Veeam Enterprise Manager. Veeam Enterprise Manager will initiate the file-level restore with the mount server without requiring the first mount.

**Note:** If guest file system indexing is disabled restores may still be initiated through Enterprise Manager however they will still require the first mount to be performed with similar performance implications as previously described.

### Veeam Explorers
Veeam Explorers are installed as part of the backup server and backup console when installed remotely. When performing item-level recoveries the file-level recovery engine is leveraged. Please see the previous section for deployment considerations.

The Veeam Explorer for SQL Server, SharePoint and Oracle all use a staging server to allow selecting a specific point in time for point-in-time restore. This introduces an additional connection as illustrated below.


![Staging Server](./pictures/backup_server_data_flow_3.png)










[Return](./Section_1/File-level_Recovery_Data_Flow.md).
