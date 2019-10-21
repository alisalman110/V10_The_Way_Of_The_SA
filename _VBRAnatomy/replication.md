# Replication

In many aspects, the replication initialization phase is similar to the
initialization phase of the backup process. Veeam Backup & Replication
starts the necessary processes, builds the list of VMs to replicate,
assigns backup infrastructure resources for the job and starts Veeam
Data Movers on two backup proxies (source and target) and the backup
repository that is used for storing replica metadata.

Next, Veeam Backup & Replication performs in-guest processing tasks,
triggers VM snapshot creation, registers a replica VM on the target host
and performs data transfer from the source host and datastore to the
target host and datastore. The source and target proxies can use one of
3 available data transport modes for reading data from source and
writing data to target.

A Veeam backup repository is used to store replica metadata.
