---
title: HAWQ Ranger Kerberos Integration
---

<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

When you have enabled Ranger Authorization for HAWQ, your HAWQ installation includes the Ranger Administrative UI and HAWQ Ranger Plug-in Service.

Specific HAWQ Ranger configuration is required when Kerberos authentication is enabled for HAWQ or for Ranger. You must configure Kerberos support for:

- HAWQ resource lookup by the Ranger Administration host during HAWQ policy definition
- HAWQ Ranger Plug-in Service communication with the Ranger Administration host for policy refresh

Use the following procedures to configure Kerberos support for your Ranger-authorized HAWQ cluster.

## Prerequisites <a id="kerb_ranger_prereq"></a>

Before you configure Kerberos for your Ranger-authorized HAWQ cluster, ensure that you have:

- Installed Java 1.7.0\_17 or later on all nodes in your cluster. Java 1.7.0_17 is required to use Kerberos-authenticated JDBC on Red Hat Enterprise Linux 6.x or 7.x.
- (Non-OpenJDK Java installations) Installed the Java Cryptography Extension (JCE) on all nodes in your cluster. 
    - If you manage your cluster with Ambari, you installed the JCE on each node before you enabled Kerberos with the Ambari **Kerberos Security Wizard**. 
    - If you manage your cluster from the command line, you must manually install the extension on these systems.
- Noted the host name or IP address of your Ranger Administration host (\<ranger-admin-node\>) and HAWQ master (\<master\>) nodes.
- Identified an existing Kerberos Key Distribution Center (KDC) or set up your KDC as described in [Install and Configure a Kerberos KDC Server](../clientaccess/kerberos.html#task_setup_kdc).
    - Note the host name or IP address of your KDC (\<kdc-server\>).
    - Note the name of the Kerberos \<realm\> in which your cluster resides.
- Enabled Ranger Authorization for HAWQ. See [Configuring HAWQ to use Ranger Policy Management](ranger-integration-config.html).


## Configure Ranger for Kerberized HAWQ<a id="ra2hawq_kerb_cfg"></a>

When you define HAWQ Ranger authorization policies, the Ranger Administration Host uses JDBC to connect to HAWQ during policy definition to look up policy resource names. When Kerberos user authentication is enabled for HAWQ, you must configure this connection for Kerberos.

To configure Ranger access to a HAWQ cluster enabled with Kerberos user authentication, you must:

- Identify an existing HAWQ administrative role or create a new HAWQ administrative role for Ranger lookup of HAWQ resources
- Create a Kerberos principal for the lookup role
- Update the Ranger HAWQ service definition

### Procedure <a id="kerb_ra2hawq_proc"></a>

Perform the following procedure to enable the Ranger Administration Host to look up resources in your kerberized HAWQ cluster. You will perform operations on the HAWQ \<master\>, \<ranger-admin-node\>, and \<kdc-server\> nodes.

1. Log in to the HAWQ master node and set up your environment:

    ``` shell
    $ ssh gpadmin@<master>
    gpadmin@master$ . /usr/local/hawq/greenplum_path.sh
    ```

2. Identify an existing HAWQ administrative role or create a new HAWQ administrative role for Ranger resource lookup. For example, to create a new administrative role:

    ``` shell
    gpadmin@master$ psql -c 'CREATE ROLE "rangerlookup_hawq" with LOGIN SUPERUSER;' 
    ```
   
    You may choose a different name for the Ranger lookup role.

3. Log in to the KDC server system and generate a principal for the HAWQ `rangerlookup_hawq` role. Substitute your Kerberos \<realm\>. For example:

    ``` shell
    $ ssh root@<kdc-server>
    root@kdc-server$ kadmin.local -q "addprinc -pw changeme rangerlookup_hawq@REALM.DOMAIN"
    ```
    
    You do not need to generate a keytab file for the `rangerlookup_hawq` principal because you will provide the password in the HAWQ service definition of the Ranger Admin UI.
    
4. Validate the `rangerlookup_hawq` role and principal by generating a ticket and attempting to connect to HAWQ. For example:

    ``` shell
    $ ssh gpadmin@<hawq-node>
    gpadmin@hawq-node$ kinit rangerlookup_hawq
    Password for rangerlookup_hawq@REALM.DOMAIN
    gpadmin@hawq-node$ psql -d postgres -p 5432 -U rangerlookup_hawq -h master
    ```

5. Start the Ranger Admin UI in a supported web browser. The default URL is \<ranger-admin-node\>:6080. 

6. Locate the HAWQ service definition and click the **Edit** button. 

7. Update the applicable **Config Properties** fields:

    **HAWQ User Name*** : Enter the HAWQ Ranger lookup role you identified or created in Step 2 above.  
    **HAWQ User Password*** : Enter the password you assigned to the HAWQ Ranger lookup role principal.  
    **HAWQ Kerberos Service Name** : Enter `postgres` or your custom HAWQ service name.  
    **HAWQ Authentication Type** : Select **Kerberos** from the drop-down list.  

8. Click the **Test Connection** button. If you connect successfully, Ranger displays a dialog with the message:

    ``` pre
    Connected Successfully.
    ```

    You successfully configured Ranger to use Kerberos authentication when looking up HAWQ resource names.

9. **Save** your changes to permanently configure Kerberos authentication for HAWQ Ranger resource lookup.


## Configure HAWQ for Kerberized Ranger<a id="rps2ra_kerb_cfg"></a>

When Ranger Authorization is enabled for HAWQ, the Ranger Plug-in Service fetches policies from the Ranger Administration host. If Kerberos is enabled for Ranger, you must specifically configure the Ranger Plug-in Service for this communication.

This procedure includes:

- Adding an administrative Ranger user named `postgres`
- Updating the HAWQ Ranger Plug-in Service configuration to utilize Kerberos


### Procedure <a id="kerb_rps2ra_proc"></a>

Perform the following steps to enable Ranger Plug-in Service policy refresh from a kerberized Ranger:

1. Create a `postgres` Ranger administrative user.

    1. Open the Ranger Admin UI. The default URL is \<ranger-admin-node\>:6080.
    2. Navigate to **Settings > Users/Groups**.
    3. Click the **Add New User** button.
    4. Fill out the required fields in the **User Detail** dialog:

        **User Name*** : Enter `postgres`.  
        **New Password*** : Enter the user's password.  
        **Password Confirm*** : Enter the password again.  
        **First Name*** : Enter the first name of the user.  
        **Select Role*** : Select the **Admin** role.

2. Update the HAWQ Ranger Plug-in Service configuration to use Kerberos authentication when communicating with Ranger.

    2. Edit the HAWQ Ranger Plug-in Service `rps.properties` configuration file and set the `RPS_AUTH` property to specify Kerberos authentication.

        ``` shell
        gpadmin@master$ vi $GPHOME/ranger/etc/rps.properties
        ```
    
        ``` pre
        RPS_AUTH=kerberos
        ``` 

    3. `rps.properties` also includes `RPS_PRINCIPAL` and `RPS_KEYTAB` properties. If these properties are unset, the Ranger Plug-in Service uses the cached Kerberos credentials that are available for the HAWQ runtime user account (typically `gpadmin`). Set these properties if the default values are not appropriate for your environment.

3. Stop the Ranger Plug-in Service:

    ``` shell
    gpadmin@master$  $GPHOME/ranger/bin/rps.sh stop
    ```

4. Generate a ticket for the `postgres@<realm>` principal:

    ``` shell
    gpadmin@master$ kinit -kt /etc/security/keytabs/hawq.service.keytab postgres@REALM.DOMAIN
    ```

5. Restart the HAWQ Ranger Plug-in Service:

    ``` shell
    gpadmin@master$  $GPHOME/ranger/bin/rps.sh start
    ```

6. If you have enabled HAWQ Ranger High Availability, synchronize the HAWQ Ranger Plug-in Service configuration files from the HAWQ master node to the standby master node as described in the [Register a Standby Ranger Plug-in Service](ranger-integration-config.html#rps_ha_cfg_standbyrps) procedure, including restarting the standby master node.

7. Verify the configuration.

    1. Examine the HAWQ Ranger Plug-in Service log file:

        ```shell
        gpadmin@master$ cd $GPHOME/ranger/plugin-service/logs
        gpadmin@master$ vi rps.log
        ```
        
        Locate the most recent entry that identifies the authentication type used:
        
        ``` pre
        2017-06-26 18:21:45,518 INFO  [org.apache.ranger.audit.queue.AuditBatchQueue0] org.apache.ranger.audit.queue.AuditBatchQueue: Running queue rps.async.batch as user postgres@REALM.DOMAIN (auth:KERBEROS)
        ```
        
        This entry should specify `auth:KERBEROS`.

    2. Create a new HAWQ policy in the Ranger Admin UI and **Save**.

    3. Examine the HAWQ Ranger Plug-in Service log file again. Locate the most recent entry that identifies a new policy version:
        
        ``` pre
        2017-06-26 18:52:02,183 INFO  [Thread-5] org.apache.ranger.plugin.util.PolicyRefresher: PolicyRefresher(serviceName=hawq): found updated version. lastKnownVersion=11; newVersion=12
        ```
        
        If you configured Kerberos correctly, you will see a `newVersion=<n>` entry in this log. If you did *not* configure this communication correctly, you will encounter error messages similar to the following:
        
        ``` pre
        2017-06-26 18:24:17,050 ERROR [Thread-6] org.apache.ranger.plugin.util.PolicyRefresher: PolicyRefresher(serviceName=hawq): failed to refresh policies. Will continue to use last known version of policies (10)
        ```