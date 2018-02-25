---
title: Pivotal HDB 2.3.0 Release Notes
---

Pivotal HDB 2.3.0 is a minor release of the product and is based on [Apache HAWQ&reg; \(Incubating\)](http://hawq.incubator.apache.org/). This release includes HAWQ Ranger High Availability and Kerberos support, Beta support for the PXF HiveVectorizedORC and JDBC profiles, and bug fixes.

## <a id="topic_dhh_2jx_yt"></a>Supported Platforms 

The supported platform for running Pivotal HDB 2.3.0 comprises:

-   Red Hat Enterprise Linux \(RHEL\) 6.4+, 7.2+ \(64-bit\) \(See note in [Known Issues and Limitations](#issues-os) for kernel limitations.\)
-   [Hortonworks Data Platform \(HDP\) 2.5.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.3/bk_release-notes/content/ch_relnotes_v253.html).
-   [Ambari 2.4.2](http://docs.hortonworks.com/HDPDocuments/Ambari-2.4.2.0/bk_ambari-release-notes/content/ch_relnotes-ambari-2.4.2.0.html) \(for Ambari-based installation and HAWQ cluster management\).

Each Pivotal HDB host machine must also meet the Apache HAWQ \(Incubating\) system requirements. [See Apache HAWQ System Requirements](../../hawq/requirements/system-requirements.html) for more information.

### <a id="topic_g53_tgv_2v"></a>Product Support Matrix

The following table summarizes Pivotal HDB product support for current and previous versions of Pivotal HDB, Hadoop, HAWQ, Ambari, and operating systems.

|Pivotal HDB Version|PXF Version |HDP Version Requirement (Pivotal HDP and Hortonworks HDP)|Ambari Version Requirement|HAWQ Ambari Plug-in Requirement|MADlib Version Requirement | RHEL/CentOS Version Requirement|SuSE Version Requirement|
|-------------------|--------------|-----------------------|--------------------------|-------------------------------|-----------------------|--------|------------------------|
|2.3.0.0|3.3.0.0|2.6.1| 2.5.1 |2.3.0.0|1.10, 1.11|6.4+, 7.2+ \(64-bit\)|n/a|
|2.2.0.0|3.2.1.0|2.5.3, 2.6.1|2.4.2, 2.5.1 |2.2.0.0|1.9, 1.9.1, 1.10|6.4+, 7.2+ \(64-bit\)|n/a|
|2.1.2.0|3.2.0.0|2.5|2.4.1, 2.4.2 |2.1.2.0|1.9, 1.9.1, 1.10|6.4+ \(64-bit\)|n/a|
|2.1.1.0|3.1.1.0|2.5|2.4.1 |2.1.1.0|1.9, 1.9.1|6.4+ \(64-bit\)|n/a|
|2.1.0.0|3.1.0.0|2.5|2.4.1 |2.1.0.0|1.9, 1.9.1|6.4+ \(64-bit\)|n/a|
|2.0.1.0|3.0.1|2.4.0, 2.4.2|2.2.2, 2.4 |2.0.1|1.9, 1.9.1|6.4+ \(64-bit\)|n/a|
|2.0.0.0|3.0.0|2.3.4, 2.4.0|2.2.2|2.0.0|1.9, 1.9.1|6.4+ \(64-bit\)|n/a|
|1.3.1.1|2.5.1.1|2.2.6|2.0.x|1.3.1|1.7.1, 1.8, 1.9, 1.9.1|6.4+|SLES 11 SP3|
|1.3.1.0|2.5.1.1|2.2.6|2.0.x|1.3.1|1.7.1, 1.8, 1.9, 1.9.1|6.4+|SLES 11 SP3|
|1.3.0.3|1.3.3|2.2.4.2|1.7|1.2|1.7.1, 1.8, 1.9, 1.9.1|6.4+|SLES 11 SP3|
|1.3.0.2|1.3.3|2.2.4.2|1.7|1.2|1.7.1, 1.8, 1.9, 1.9.1|6.4+|SLES 11 SP3|
|1.3.0.1|1.3.3|2.2.4.2|1.7|1.1|1.7.1, 1.8, 1.9, 1.9.1|6.4+|n/a|
|1.3.0.0|1.3.3|n/a|n/a|n/a|1.7.1, 1.8, 1.9, 1.9.1|n/a|n/a|


### <a id="topic_plsupportmatrix"></a>Procedural Language Support Matrix

The following table summarizes component version support for Procedural Languages available in Pivotal HDB 2.x. The versions listed have been tested with Pivotal HDB. Higher versions may be compatible. Please test higher versions thoroughly in your non-production environments before deploying to production.

|Pivotal HDB Version|PL/Java Java Version Requirement|PL/R R Version Requirement|PL/Perl Perl Version Requirement|PL/Python Python Version Requirement|
|-------------------|-----------------------|--------------------------|-------------------------------|-------------------------------|
|2.3.0.0|1.7| 3.3.1 |5.10.1|2.6.2|
|2.2.0.0|1.7| 3.3.1 |5.10.1|2.6.2|
|2.1.x.0|1.7| 3.3.1 |5.10.1|2.6.2|
|2.0.1.0|1.7|3.3.1 |5.10.1|2.6.2|
|2.0.0.0|1.6, 1.7|3.1.0|5.10.1|2.6.2|


### <a id="topic_ywd_4fv_2v"></a>AWS Support Requirements 

Pivotal HDB is supported on Amazon Web Services \(AWS\) servers using either Amazon block level Instance store \(Amazon uses the volume names ephemeral\[0-23\]\) or Amazon Elastic Block Store \(Amazon EBS\) storage. Use long-running EC2 instances with these for long-running HAWQ instances, as Spot instances can be interrupted. If using Spot instances, minimize risk of data loss by loading from and exporting to external storage. See [Amazon EC2 Configuration](../../hawq/install/aws-config.html) for additional HAWQ AWS-related deployment considerations.


## <a id="whatsnewintherelease"></a> Pivotal HDB 2.3.0 Features and Changes 

Pivotal HDB 2.3.0 is based on [Apache HAWQ \(Incubating\)](http://hawq.incubator.apache.org/), and includes the following new features as compared to Pivotal HDB 2.2.0:

- *HAWQ Ranger High Availability*

    HDB 2.3 allows you to configure a standby HAWQ Ranger Plugin Service (RPS) for failover in cases where the primary RPS is unavailable or not responding on the local HAWQ Master node. Configuring a standby RPS ensures that HAWQ can authorize incoming requests at all times without any downtime. Refer to the [High Availability and HAWQ Ranger](../../hawq/ranger/ranger-ha.html) documentation for additional information.

- *HAWQ Ranger Kerberos Support*

    HDB 2.3 supports HAWQ Ranger integration when Kerberos is enabled for Ranger and/or Kerberos is enabled for HAWQ user authentication. See the [HAWQ Ranger Kerberos Integration](../../hawq/ranger/ranger-kerberos.html) documentation for the information necessary to configure Kerberos support for your Ranger-authorized HAWQ cluster.

- *PXF HiveVectorizedORC profile*

    HDB 2.3 includes a Beta release of Optimized Row Columnar (ORC) file format with Vectorized Batch Reader support. Refer to the PXF Hive plug-in [ORC File Format](../../hawq/pxf/HivePXF.html#hiveorc-intro) documentation for specific information related to this new feature.

- *PXF JDBC plug-in*

    HDB 2.3 includes a Beta release of the PXF JDBC plug-in. With this plug-in, you can create and query a HAWQ external table representing an external SQL data store using a JDBC connection. Refer to the [Accessing External SQL Databases with JDBC (Beta)](../../hawq/pxf/JdbcPXF.html) documentation for specific information related to the PXF JDBC plug-in.

- *Getting Started with HAWQ Tutorial*

    HDB 2.3 includes the [Getting Started with HAWQ Tutorial](../../hawq/tutorial/overview.html). This guide provides a quick introduction and various exercises to get you up and running with your HAWQ installation. You will learn about the HAWQ runtime environment, including cluster and database adminstration basics. You will also use HAWQ and PXF to access sample managed and unmanaged data.

## <a id="hdb20121xmigrate"></a>Pivotal HDB 2.3.0 Upgrade 

The [**Upgrading from Pivotal HDB 2.1.x or 2.2**](../install/HDB21xUpgrade.html) guide provides specific details on applying the Pivotal HDB 2.3.0 minor release to your HDB 2.1.x or 2.2.0 installation.

**Note**: Direct upgrades from HDB 2.0.x are not supported. There is also no direct upgrade path from Pivotal HDB 1.x to HDB 2.3.0. For more information on considerations for upgrading from Pivotal 1.x releases to Pivotal 2.x releases, refer to the [Pivotal HDB 2.0 documentation](http://hdb.docs.pivotal.io/200/hdb/releasenotes/HAWQ20ReleaseNotes.html#upgradepaths). Contact your Pivotal representative for assistance in migrating from HDB 1.x to HDB 2.x.

## <a id="haqhdbdiffs"></a>Differences Compared to Apache HAWQ \(Incubating\)

Pivotal HDB 2.3.0 includes all of the functionality in [Apache HAWQ \(Incubating\)](http://hawq.incubator.apache.org/).

## <a id="resolvedissues"></a>Resolved Issues
The following HAWQ and PXF issues were resolved in Pivotal HDB 2.3.0.

| Apache Jira | Component | Summary |
| ----------- | --------- | ------- |
| [HAWQ-1404](https://issues.apache.org/jira/browse/HAWQ-1404) | PXF| PXF now leverages the file-level statistics of an ORC file, and emits records for `COUNT(*)` |
| [HAWQ-1409](https://issues.apache.org/jira/browse/HAWQ-1409) | PXF | HAWQ sends an additional header to PXF to indicate the aggregate function type |
| [HAWQ-1417](https://issues.apache.org/jira/browse/HAWQ-1417) | Core | Fixed a crash that could occur when executing `ANALYZE` after `COPY` |
| [HAWQ-1422](https://issues.apache.org/jira/browse/HAWQ-1422) | Security | HAWQ now provides user-group membership in the Ranger Plug-in Service when requesting access |
| [HAWQ-1425](https://issues.apache.org/jira/browse/HAWQ-1425) | Command Line Tools | Fixed an incorrect init cluster error message that occurred when an ssh connection failed |
| [HAWQ-1426](https://issues.apache.org/jira/browse/HAWQ-1426) | Command Line Tools | Fixed a problem where `hawq extract` returned an error after the table was reorganized |
| [HAWQ-1427](https://issues.apache.org/jira/browse/HAWQ-1427) | PXF | Resolved a PXF JSON Profile `lang3` dependency error |
| [HAWQ-1429](https://issues.apache.org/jira/browse/HAWQ-1429) | PXF | PXF no longer uses AggBridge when a `WHERE` clause is specified |
| [HAWQ-1430](https://issues.apache.org/jira/browse/HAWQ-1430) | Security | Updated the Ranger-related log level to avoid log flooding |
| [HAWQ-1431](https://issues.apache.org/jira/browse/HAWQ-1431) | PXF | PXF no longer uses `StatsAccessor` when a column is specified in a `SELECT` clause |
| [HAWQ-1433](https://issues.apache.org/jira/browse/HAWQ-1433) | Resource Manager | `ALTER RESOURCE QUEUE` DDL now checks the format of the attributes MEMORY\_CLUSTER\_LIMIT and CORE\_CLUSTER\_LIMIT |
| [HAWQ-1434](https://issues.apache.org/jira/browse/HAWQ-1434) | PXF | Removed forced upper casing of the table name in the PXF JDBC plug-in |
| [HAWQ-1436](https://issues.apache.org/jira/browse/HAWQ-1436) | Security | Implemented Ranger Plug-in Service High Availability on HAWQ |
| [HAWQ-1438](https://issues.apache.org/jira/browse/HAWQ-1438) | Core | HAWQ now supports a resource owner beyond the transaction boundary |
| [HAWQ-1439](https://issues.apache.org/jira/browse/HAWQ-1439) | Resource Manager | HAWQ now tolerates the system time being changed to an earlier point when checking the resource context timeout |
| [HAWQ-1440](https://issues.apache.org/jira/browse/HAWQ-1440) | PXF | `ANALYZE` is supported for Hive External Tables |
| [HAWQ-1443](https://issues.apache.org/jira/browse/HAWQ-1443) | Security | Implemented Ranger lookup for HAWQ when Kerberos is enabled |
| [HAWQ-1446](https://issues.apache.org/jira/browse/HAWQ-1446) | PXF | Introduced the Vectorized profile for ORC |
| [HAWQ-1449](https://issues.apache.org/jira/browse/HAWQ-1449) | Command Line Tools | HAWQ start/stop cluster now starts/stops the Ranger Plugin Service on standby node |
| [HAWQ-1451](https://issues.apache.org/jira/browse/HAWQ-1451) | Command Line Tools| The `hawq state` command reports the status of both the Ranger Plugin Service and the Standby Ranger Plugin Service |
| [HAWQ-1452](https://issues.apache.org/jira/browse/HAWQ-1452) | Security | Removed hawq\_rps\_address_suffix and hawq\_rps\_address\_host in `hawq-site.xml` to simplify configuration for Ranger Plug-in Service High Availability |
| [HAWQ-1453](https://issues.apache.org/jira/browse/HAWQ-1453) | Core | Fixed a problem that caused the error:  relation_close() report error at analyzeStmt(): is not owned by resource owner TopTransaction (resowner.c:814) |
| [HAWQ-1455](https://issues.apache.org/jira/browse/HAWQ-1455) | Core | Fixed incorrect results on CTAS query over catalog |
| [HAWQ-1456](https://issues.apache.org/jira/browse/HAWQ-1456) | Security | HAWQ copies Ranger Plugin Service configuration files to the standby in specific scenarios |
| [HAWQ-1457](https://issues.apache.org/jira/browse/HAWQ-1457) | Core | Shared memory for SegmentStatus and MetadataCache is no longer allocated on segments |
| [HAWQ-1460](https://issues.apache.org/jira/browse/HAWQ-1460) | Core | The WAL Send Server process now exits if postmaster on master is killed |
| [HAWQ-1461](https://issues.apache.org/jira/browse/HAWQ-1461) | PXF | Improved partition parameter validation for the PXF-JDBC plugin |
| [HAWQ-1476](https://issues.apache.org/jira/browse/HAWQ-1476) | Security | Improved `enable-ranger-plugin.sh` to support Kerberos |
| [HAWQ-1477](https://issues.apache.org/jira/browse/HAWQ-1477) | Security | Ranger Plugin Service connects to Ranger admin under kerberos security |
| [HAWQ-1480](https://issues.apache.org/jira/browse/HAWQ-1480) | Command Line Tools | Packing a core file in HAWQ is now possible |
| [HAWQ-1485](https://issues.apache.org/jira/browse/HAWQ-1485) | Security | The user/password is used instead of the credentials cache in Ranger lookup for HAWQ when Kerberos is enabled |
| [HAWQ-1486](https://issues.apache.org/jira/browse/HAWQ-1486) | PXF | Resolved a PANIC that occurred while accessing a PXF HDFS table |
| [HAWQ-1487](https://issues.apache.org/jira/browse/HAWQ-1487) | Core | Fixed a deadlock that occurred when trying to process an interrupt in error handling |
| [HAWQ-1492](https://issues.apache.org/jira/browse/HAWQ-1492) | PXF | Packaged the jdbc-plugin RPM with PXF installation |
| [HAWQ-1493](https://issues.apache.org/jira/browse/HAWQ-1493) | Security | Integrated the Ranger lookup JAAS configuration in the ranger-admin plugin JAR |


## <a id="knownissues"></a>Known Issues and Limitations 

### <a id="issues-os"></a>Operating System
-   HDB installations running RHEL-7 or CentOS 7 versions prior to version 7.3 may experience an operating system issue that could cause HDB to hang with large workloads. RHEL 7.3 and CentOS 7.3 resolve this issue.
  
### <a id="issues-ranger"></a>Ranger Integration with HAWQ
-   Refer to [Limitations of Ranger Policy Management](../../hawq/ranger/ranger-overview.html#limitations) for a discussion of limitations related to HAWQ integration with Ranger authorization.

### <a id="issues-pxf"></a>PXF
-   GPSQL-3345 - To take advantage of the change in number of virtual segments, PXF external tables must be dropped and recreated after updating the `default_hash_table_bucket_number` server configuration parameter.
-   GPSQL-3347 - The `LOCATION` string provided when creating a PXF external table must use only ASCII characters to identify a file path. Specifying double-byte or multi-byte characters in a file path returns the following error (formatted for clarity):

    ``` pre
    ERROR: remote component error (500) from 'IP_Address:51200': type Exception report
      message:  File does not exist: /tmp/??????/ABC-??????-001.csv
      description:  The server encountered an internal error that prevented it from fulfilling this request.
      exception:  java.io.IOException: File does not exist: /tmp/??????/ABC-??????-001.csv (libchurl.c:897) (seg10 hdw2.hdp.local:40000 pid=389911) (dispatcher.c:1801)
    ```
-   PXF in a Kerberos-secured cluster requires YARN to be installed due to a dependency on YARN libraries.
-   In order for PXF to interoperate with HBase, you must manually add the PXF HBase JAR file to the HBase classpath after installation. See [Post-Install Procedure for Hive and HBase on HDP](../install/install-ambari.html#post-install-pxf).
-   [HAWQ-974](https://issues.apache.org/jira/browse/HAWQ-974) - When using certain PXF profiles to query against larger files stored in HDFS, users may occasionally experience hanging or query timeout. This is a known issue that will be improved in a future HDB release.  Refer to [Addressing PXF Memory Issues](../../hawq/pxf/TroubleshootingPXF.html#pxf-memcfg) for a discussion of the configuration options available to address these issues in your PXF deployment.
-   The `HiveORC` profile supports aggregate queries (count, min, max, etc.), but they have not yet been optimized to leverage ORC file- and stripe-level metadata.
-   The `HiveVectorizedORC` profile does not support the timestamp data type or complex types.


### <a id="issues-install"></a>Ambari

-  Ambari-managed clusters should only use Ambari for setting server configuration parameters. Parameters modified using the `hawq config` command will be overwritten on Ambari startup or reconfiguration.
-   When installing HAWQ in a Kerberos-secured cluster, the installation process may report a warning/failure in Ambari if the HAWQ configuration for resource management type is switched to YARN mode during installation. The warning is related to HAWQ not being able to register with YARN until the HDFS & YARN services are restarted with new configurations resulting from the HAWQ installation process.
-   The HAWQ standby master will not work after you change the HAWQ master port number. To enable the standby master you must first remove and then re-initialize it. See [Removing the HAWQ Standby Master](../../hawq/admin/ambari-admin.html#amb-remove-standby) and [Activating the HAWQ Standby Master](../../hawq/admin/ambari-admin.html#amb-activate-standby).
-   The Ambari **Re-Synchronize HAWQ Standby Master** service action fails if there is an active connection to the HAWQ master node. The HAWQ task output shows the error, `Active connections. Aborting shutdown...` If this occurs, close all active connections and then try the re-synchronize action again.
-   The Ambari **Run Service Check** action for HAWQ and PXF may not work properly on a secure cluster if PXF is not co-located with the YARN component.
-   In a secured cluster, if you move the YARN Resource Manager to another host you must manually update `hadoop.proxyuser.yarn.hosts` in the HDFS `core-site.xml` file to match the new Resource Manager hostname. If you do not perform this step, HAWQ segments fail to get resources from the Resource Manager.
-   The Ambari **Stop HAWQ Server (Immediate Mode)** service action or `hawq stop -M immediate` command may not stop all HAWQ master processes in some cases. Several `postgres` processes owned by the `gpadmin` user may remain active.
-   Ambari checks whether the `hawq_rm_yarn_address` and `hawq_rm_yarn_scheduler_address` values are valid when YARN HA is not enabled. In clusters that use YARN HA, these properties are not used and may get out-of-sync with the active Resource Manager. This can leading to false warnings from Ambari if you try to change the property value.
-  Ambari does not support Custom Configuration Groups with HAWQ.
- Certain HAWQ server configuration parameters related to resource enforcement are not active. Modifying the parameters has no effect in HAWQ since the resource enforcement feature is not currently supported. These parameters include `hawq_re_cgroup_hierarchy_name`, `hawq_re_cgroup_mount_point`, and `hawq_re_cpu_enable`. These parameters appear in the **Advanced hawq-site** configuration section of the Ambari management interface.

#### <a id="nn-workaround"></a>Workaround Required after Moving Namenode
If you use the Ambari Move Namenode Wizard to move a Hadoop namenode, the Wizard does not automatically update the HAWQ configuration to reflect the change. This leaves HAWQ in an non-functional state, and will cause HAWQ service checks to fail with an error similar to:

<pre><code>
2017-04-19 21:22:59,138 - SQL command executed failed: export PGPORT=5432 && source
/usr/local/hawq/greenplum_path.sh && psql -d template1 -c \\\\\"CREATE  TABLE
ambari_hawq_test (col1 int) DISTRIBUTED RANDOMLY;\\\\\"
Returncode: 1
Stdout:
Stderr: Warning: Permanently added 'ip-10-32-36-168.ore1.vpc.pivotal.io,10.32.36.168'
(RSA) to the list of known hosts.
WARNING:  could not remove relation directory 16385/1/18366: Input/output error
CONTEXT:  Dropping file-system object -- Relation Directory: '16385/1/18366'
ERROR:  could not create relation directory
hdfs://ip-10-32-36-168.ore1.vpc.pivotal.io:8020/hawq_default/16385/1/18366: Input/output error
<br/>
2016-04-19 21:22:59,139 - SERVICE CHECK FAILED: HAWQ was not able to write and query
from a table
2016-04-19 21:23:02,608 - ** FAILURE **: Service check failed 1 of 3 checks
stdout:   /var/lib/ambari-agent/data/output-281.txt
</code></pre>

To work around this problem, perform one of the following procedures after you complete the Move Namenode Wizard.

##### Workaround for Non-HA NameNode Clusters:

1. Perform an HDFS service check to ensure that HDFS is running properly after you moved the NameNode.
2. Use the Ambari `config.sh` utility to update `hawq_dfs_url` to the new NameNode address. See the [Modify configurations](https://cwiki.apache.org/confluence/display/AMBARI/Modify+configurations) on the Ambari Wiki for more information. For example:

    ``` shell
    $ cd /var/lib/ambari-server/resources/scripts/
    $ ./configs.sh set {ambari_server_host} {clustername} hawq-site
    $ hawq_dfs_url {new_namenode_address}:{port}/hawq_default
    ```
    
3. Restart the HAWQ configuration to apply the configuration change.
4. Use `ssh` to log into a HAWQ node and run the `checkpoint` command:

    ``` shell
    $ psql -d template1 -c "checkpoint"
    ```
6. Stop the HAWQ service.
7. The master data directory is identified in the `$GPHOME/etc/hawq-site.xml` file `hawq_master_directory` property value. Copy the master data directory to a backup location:

    ``` shell
    $ export MDATA_DIR=/value/from/hawqsite
    $ cp -r $MDATA_DIR /catalog/backup/location
    ```
8. Execute this query to display all available HAWQ filespaces:
 
    ``` sql
    SELECT fsname, fsedbid, fselocation FROM pg_filespace AS sp,
pg_filespace_entry AS entry, pg_filesystem AS fs WHERE sp.fsfsys = fs.oid
AND fs.fsysname = 'hdfs' AND sp.oid = entry.fsefsoid ORDER BY
entry.fsedbid;
    ```
    ``` pre
          fsname | fsedbid | fselocation
-------------+---------+------------------------------------------------
cdbfast_fs_a | 0       | hdfs://hdfs-cluster/hawq//cdbfast_fs_a
dfs_system   | 0       | hdfs://test5:9000/hawq/hawq-1459499690
(2 rows)
    ```
9. Execute the `hawq filespace` command on each filespace that was returned by the previous query. For example:

    ``` shell
    $ hawq filespace --movefilespace dfs_system --location=hdfs://new_namenode:port/hawq/hawq-1459499690
    $ hawq filespace --movefilespace cdbfast_fs_a --location=hdfs://new_namenode:port/hawq//cdbfast_fs_a
    ```
9. If your cluster uses a HAWQ standby master, reinitialize the standby master in Ambari using the Remove Standby Wizard followed by the Add Standby Wizard.
9. Start the HAWQ Service.
9. Run a HAWQ service check to ensure that all tests pass.

##### Workaround for HA NameNode Clusters:

1. Perform an HDFS service check to ensure that HDFS is running properly after you moved the NameNode.
2. Use Ambari to expand `Custom hdfs-client` in the HAWQ Configs tab, then update the `dfs.namenode.` properties to match the current NameNode configuration.
3. Restart the HAWQ configuration to apply the configuration change.
9. Run a HAWQ service check to ensure that all tests pass.
