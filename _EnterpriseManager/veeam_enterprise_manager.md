<!--- This was last Changed 03-05-17 by PS --->
# Veeam Backup Enterprise Manager

## Whether to Deploy?
Enterprise Manager is intended for centralized reporting
and management of multiple backup servers. It provides
delegated restore and self-service capabilities as well as the
ability for users to request Virtual Labs from backup
administrators. It provides a central management point for multiple
backup servers from a single interface. Enterprise Manager is also a part of
the data encryption and decryption processes implemented in the Veeam
solution and best practice recommend deploying Enterprise Manager in
the following scenarios:

-   It is recommended to deploy Enterprise Manager if you are using
    encryption for backup or backup copy jobs. If you have enabled
    password loss protection (https://helpcenter.veeam.com/docs/backup/em/em_manage_keys.html?ver=95)
    for the connected backup servers backup files will be
    encrypted with an additional private key which is unique for each
    instance of Enterprise Manager. This will allow Enterprise Manager
    administrators to unlock backup files using a challenge/response
    mechanism effectively acting as a Public Key Infrastructure (PKI).

-   If an organization has a Remote Office/Branch Office (ROBO)
    deployment then leverage Enterprise Manager to provide site
    administrators with granular restore access via web UI (rather than
    providing access to Backup & Replication console).

-   In enterprise deployments delegation capabilities can be used to
    elevate the 1st line support to perform in-place restores without
    administrative access.

-   For deployments spanning multiple locations with stand-alone
    instances of Enterprise Manager will be
    helpful in managing licenses across these instances to
    ensure compliance.

-   Searching the Indexes can also be used to find files that
have been backed up and the indexes stored in the Enterprise Manager database.    

-   Enterprise Manager is required when automation is essential to
    delivering IT services — to provide access to the Veeam RESTful API.

If the environment includes a single instance of
Backup & Replication you may not need to deploy Enterprise
Manager, especially if you want to avoid additional SQL Server database
activity and server resource consumption (which can be especially
important if using SQL Server Express Edition).

**Note:** If Enterprise Manager is not deployed, password loss
protection will be unavailable.

## Self-Service File Restore

In addition to 1-Click File-Level Restore Backup & Replication
allows VM administrators to restore files or folders from a VM guest OS
using a browser from within the VM guest OS, without creating specific
users or assigning them specific roles at the Veeam Enterprise Manager
level. To do this an administrator of the VM can access the self-service
web portal using the default URL: "https://ENTERPRISE_MANAGER:9443/selfrestore".
