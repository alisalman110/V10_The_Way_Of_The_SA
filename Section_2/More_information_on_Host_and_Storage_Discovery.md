# **More information on Host and Storage Discovery**

To collect information about the virtual infrastructure all managed vCenters and their connected hosts and datastores are periodically rescanned.   
This rescan process is visible in the **History** tab > **System** section in the Veeam Backup & Replication console.  
The Host discovery process runs every four hours. All the collected information is stored within the configuration database.


The amount of collected information is typically very small however the Host discovery process may take longer or even exceed the default schedule in highly distributed environments.  
If hosts or clusters are connected to vCenter over a high-latency link you may consider deploying a Backup server locally on the ROBO, then you can create a vCenter service account with a limited scope to that particular location in order to reduce the window of the Host discovery process. If the ROBO uses a stand-alone host it is possible to add the host as a managed server directly instead of through vCenter.  

**Note:** Avoid adding individual hosts to the backup infrastructure if using shared storage in a vSphere cluster.



If storage with advanced integration (HPE, NetApp, EMC, Nimble) are added to the **Storage Integration** tab there will additionally be a Storage discovery process periodically rescanning storage hourly. This process checks all snapshots for virtual machine restore points for usage within Veeam Explorer for Storage Snapshots. The Veeam Backup & Replication server itself will not perform the actual scanning of volumes but it will use the management API's of the storage controller to read information about present snapshots. Only proxy servers with required storage paths available will be used for the actual storage rescanning process[^2].


The following table shows the three different scanning workflows:

  | Adding new storage controller                       |  Creating new snapshot                              | Automatic scanning                                  |
  | ----------------------------------------------------|-----------------------------------------------------| ----------------------------------------------------|
  | 1. Collect specific storage information             |  1. Creating new Snapshot                           | 1. Storage Monitor runs in background               |
  | 2. List of volumes, snapshots, LUNs and NFS exports |  2. Lists initiators                                | 2. Detecting new volumes                            |
  | 3. Checking licenses, FC and iSCSI server           |  3. Testing iSCSI, NFS and FC from proxies          | 3. Scanning volumes for snapshots every 10 minutes  |
  | 4. Lists initiators                                 |  4. Searching storage exports in VMware             | 4. Lists initiators                                 |
  | 5. Searching storage exports in VMware              |  5. Mapping discovered VMs from datastores to snapshots  | 5. Testing iSCSI, NFS and FC from proxies           |
  | 6. Mapping discovered VMs from datastores to snapshots   |  6. Export and scan the snapshots with proxies      | 6. Searching storage exports in VMware              |
  | 7. Export and scan the snapshots with proxies       |  7. Update configuration database                   | 7. Mapping discovered VMs from datastores to snapshots   |
  | 8. Update configuration database                    |                                                     | 8. Export and scan the discovered objects with proxies |
  |                                                     |                                                     | 9. Update configuration database                            |

The scan of a storage controller performs, depending on the protocol,
several tasks on the storage operating system. Therefore it is
recommended to have some performance headroom on the controller. If your
controller is already running on >90% CPU utilization, keep in mind
that the scan might take significant time to complete.

The scanning interval of 10 minutes and 7 days can be changed with the following
registry keys.

-   Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Veeam\Veeam Backup and Replication`
-   Key: `SanMonitorTimeout`
-   Type: REG_DWORD
-   Default value: 600
-   Defines in seconds how frequent we should monitor SAN infrastructure and
    run incremental rescan in case of new new instances


-   Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Veeam\Veeam Backup and Replication`
-   Key: `SanRescan_Periodically_Days`
-   Type: REG_DWORD
-   Default value: 7
-   Defines in days how frequent we should initiate periodic full rescan after
    Veeam Backup service rescan

Per default Veeam will scan all volumes and LUNs on the storage
subsystem. During rescan, each present snapshot produces a snapshot
clone, mounts to a proxy server, scans the filesystem, lookup for
discovered VMs and unmounts. This is repeated for every present snapshot.

**Example**: A storage system with 50 volumes or LUNs with 10 snapshots for each.
Scanning the entire system means 500 (50x10) mounts and clones are
performed. Depending on the performance of
the storage system and the proxy server, this can take significant time.

To minimize the scan time it is recommended to select the volumes used
by VMware within the setup wizard to avoid the overhead of scanning
unused data volumes.














[Return](../Section_1/Section_1/Host_and_Storage_Discovery.md).
