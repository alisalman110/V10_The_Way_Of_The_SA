# Instant VM Recovery

## Performance concerns

### Read pattern

Usually reseved for the guests requiring the best possible RTOs, the IVMR process is read intensive and its performance is directly related to the performance of the underlying repository. Very good results can obtained from standard drives repositories (sometimes even offering faster boot time than the production guest) while deduplication appliances might be considered carefully for such kind of use.

Keep in mind that when working on its backup files to start the guest, Veeam Backup and Replication needs to access the metadatas, which is generating some random small blocks read pattern on the repository.

### Write redirections

Once booted, the guest will read existing blocks from the backup storage and write/re-read new blocks on the configured storage (whether beeing a datastore or a temporary file on the vPower NFS server local drive  in the folder "%ALLUSERSPROFILE%\Veeam\Backup\NfsDatastore"). To ensure consistent performances during the IVMR process, it is recommended to redirect the writes of the restored guest on a production datastore.