# Partitions (Workflows)

Workflows are partitioned by account because all of our work is per-account (it'll need to be evaluated per account at some point in the message flow) and the distribution will be 'good enough'.

## Background

Account partitioning verses device partitioning for workflows.

### Partitioning by account

#### Message flow

```none
+-----------+         +-------------------------+       +-------------------+
|Ingest     ----------> Statuses                --------> Notifications     |
|           |         +-------------------------+       |                   |
|           |         +-------------------------+       |                   |
|           ----------> Activities              -------->                   |
|           |         +-------------------------+       |                   |
|           |         +-------------------------+       |                   |
|           ----------> Emergencies             -------->                   |
|           |         +-------------------------+       |                   |
|           |         +-------------------------+       |                   |
|           ----------> Etcetera                -------->                   |
+-----^-----+         +-------------------------+       +---------^---------+
      |                                                           |          
      |                                                           |          
      |                                                 +-------------------+
      |                                                 | Accounts          |
      |                                                 |                   |
      |                                                 |                   |
      |                                                 |                   |
      ---------------------------------------------------                   |
                                                        |                   |
                                                        |                   |
                                                        |                   |
                                                        |                   |
                                                        |                   |
                                                        +-------------------+
```

#### Effects

* [+] Workflow services can be accessed directly,
    because configurations are set per account.  
* [+] Some pre-workflow actions might need to be performed anyway, e.g. decompression.
* [+] Can add workflows which aaggregates across devices, e.g. groups of devices.
* [-] 'Ingest' is a single point of failure (albeit partitioned...).
* [-] Adds an extra two network hops for device messages.
* [-] Poor load distribution,
    because some accounts may have many more devices than others.

### Partitioning by device

#### Message flow

```none
+-----------+         +-------------------------+       +-------------------+
| Device    ----------> Statuses                --------> Notifications     |
| message   |         +-------------------------+       |                   |
| bus (ext) |         +-------------------------+       |                   |
|           ----------> Activities              -------->                   |
|           |         +-------------------------+       |                   |
|           |         +-------------------------+       |                   |
|           ----------> Emergencies             -------->                   |
|           |         +-------------------------+       |                   |
|           |         +-------------------------+       |                   |
|           ----------> Etcetera                -------->                   |
+-----------+         +------------^------------+       +---------^---------+
                                   |                              |          
                                   |                              |          
                                   |                    +-------------------+
                                   |                    | Accounts          |
                                   |                    |                   |
                                   |                    |                   |
                                   |                    |                   |
                                   ----------------------                   |
                                                        |                   |
                                                        |                   |
                                                        |                   |
                                                        |                   |
                                                        |                   |
                                                        +-------------------+
```

#### Effects

* [+] Minimal network hops for device messages.
* [+] Good load distribution because all devices generate messages at a similar rate.
* [-] Sagas (cross-service transactions) required,
  so that we ensure configurations are cleaned up after a device is removed from an accounts.
* [-] Forces 'notifications' to become partitioned by device or
    requires look-up of account in-between.