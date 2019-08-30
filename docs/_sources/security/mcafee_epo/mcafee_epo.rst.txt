##########
McAfee ePO
##########

**Abstract:** This document details the requirements, methodologies, and
tools required to integrate McAfee’s (now Intel) ePolicy Orchestrator
with Extreme Networks Management Center.

.. contents:: Contents
   :depth: 3

************
Introduction
************

Combining Extreme Networks Control (NAC) solution with McAfee’s (now
Intel) ePolicy Orchestrator (ePO) allows network and security
administrators to ensure that only compliant devices are able to use the
network and its resources. Devices which are out-of-date can be forced
to update their signature database automatically and optionally also get
quarantined in case of non-compliance.

Devices are compliant

-  If they have McAfee VirusScan Enterprise (VSE) running with an
   updated signature database (DAT version) or

-  If they have McAfee EndPoint Security (ENS) running and their AMCore
   status is “compliant”

*************
Prerequisites
*************

To utilize the full power of the ePO integration, the following
prerequisites are required:

Solution Components:

-  Extreme Management Center (XMC): The minimum version required is
      Extreme Management Center 8.1

   -  Licensing: NMS-ADV (Advanced License): In order to use the Extreme
      Connect solution, the advanced license needs to be installed on
      the Extreme Management server.

-  Extreme Connect: Needs to be installed and running on the Extreme
      Management server. With Extreme Management 7.0 and above all
      Connect integrations (including the ePO integration) are installed
      and updated automatically.

-  Extreme Control (NAC): Needs to be deployed and fully functional with
      minimum version of 8.1

-  McAfee (now Intel) ePolicy Orchestrator:

   -  McAfee ePO extension from Extreme Networks installed

   -  Server needs to be deployed and agents rolled out to the
         end-systems:

      -  ePO Server: 5.9.0 or above

      -  ePO agent: 5.0.6 or above

   -  Tested VirusScan Enterprise (VSE) version: 8.8.0

   -  Tested EndPoint Security (ENS) version: 10.5.3

   -  Administrator Account needs to be provided that can read all data,
         access the API and trigger client update tasks (if this feature
         is used)


********************
Integration Overview
********************

The graphic below highlights the major components necessary for this
integration to work and the benefits it provides.

-  Hardware: Any switch or WLAN solution that can perform RADIUS
   authentication for network devices and is supported by Extreme’s NAC
   solution

-  Software Products: Extreme Management Center and Control (NAC)
   solution need to be deployed on top of the network infrastructure.
   Ensure the end-systems where the McAfee agent is installed are also
   connected through wired/wirless ports where RADIUS authentication via
   NAC is implemented.

-  Extreme Connect: Starting with 7.0, all Connect modules are
   integrated with Management Center and thus automatically deployed as
   soon as an Advanced license is available. Both the McAfeeEPO and
   Extreme Control modules are required at minimum for this integration
   to work. More benefits are achieved by also utilizing the Assessment
   Adapter which also is part of the Connect installation.

-  Benefits:

   -  End-System Group: Automatic grouping of devices managed in ePO
      within NAC improves visibility and operational processes. An
      administrator can easily distinguish devices managed by ePO from
      those which are not and assign corresponding policies.

   -  Custom Data: Automatically augments NAC’s database with detailed
      device information from ePO like the DAT version, device name,
      operating system, and more. Administrators and help desk staff can
      use OneView as a single pain of glass to get a deep insight into
      end-system data.

   -  Username: Populates the username retrieved from ePO within NAC.
      This provides user context to NAC without the need to enable
      802.1X authentication mechanisms. This feature can also be
      disabled.

   -  Device Type: Populates the more granular and reliable operating
      system information retrieved from ePO within NAC.

   -  XMC Events: generate a Management Center event for each McAfee
      end-system which has a DAT version older than x days
      (configurable) or is non-compliant (based on AMCore status). Those
      Extreme Management Center events can be used to alarm admins about
      end-systems which are out-of-date and need attention.

   -  Quarantine: automatically quarantine end-systems which have a DAT
      version older than x days (configurable) or are non-compliant
      (based on AMCore status). This prevents out-of-date and thus
      potentially infected end-systems to effect and harm other
      end-systems or network resources.

   -  ePO Update Task: triggers a client update task on the McAfee ePO
      server for each end-system which has a DAT version older than x
      days (configurable) or is non-compliant (based on AMCore status).
      Ensures that all McAfee managed end-systems are update as soon as
      possible.

   -  ePO Agent Wakeup Task: triggers an agent wakeup task after
      triggering and update task and waiting for the update to finish.
      The agent wakeup task will force the agent to immediatley update
      its latest DAT version status with the ePO server and thus enable
      Connect to get the latest status much quicker in order to release
      quarantined end-systems into the normal ACCEPT state.

Overall Integration Considerations
==================================

The ePO integration with NAC supports different overall deployment
models with varying levels of security and functional benefits:

-  Best:

   -  NAC: Deploy 802.1X (TLS or PEAP) for all devices that support it
      and MAC authentication for other devices. Use policies/ACLs/VLANs
      for enforcement of different device/user groups. Use guest portal
      and captive portal for remediation of out-of-date devices.

   -  ePO Integration:

      -  Import data on devices including devicetype (not the username,
         since this is now derived from the 1X authentication) from ePO

      -  Automatically trigger a client update task for non-compliant
         devices.

      -  Automatically trigger an agent wakeup task for non-compliant
         devices and release it from the quarantine state in NAC.

      -  Generate Extreme Management Center events/alarms for all McAfee
         end-systems which are non-complinat to infrom the network and
         ePO admins about devices that are missing regular updates.
         Measures to prevent end-systems to get quarantined can be taken
         based on these alarms.

      -  Use assessment to also quarantine non-compliant devices. For
         most implementations it is **not** recommended to use a
         different VLAN for the assessment phase. This would require too
         many VLAN changes which could result in more help desk calls
         due to the complexity of this process. If you want to restrict
         your users while in assessment, use a specific assessment
         policy/ACL instead.

   -  Benefits:

      -  Highest level of security with secure authentication (1X), ePO
         and NAC visibility and enforcement with integrated workflows

      -  Automatic live inventory of your whole network infrastructure
         (not only ePO-managed devices) with even more extra data added
         from ePO (visibility, troubleshooting, support)

      -  Guest support

-  Better:

   -  NAC: Deploy with MAC authentication only (no 1X, portal for
      guests) but use policies/ACLs/VLANs for enforcement of different
      device groups

   -  ePO Inegration: Import data on devices including devicetype and
      username from ePO and automatically trigger a client update task
      for non-compliant devices.

      -  Automatically trigger a client update task for devices which
         have a VSE DAT version older than X versions from the current
         master catalog.

      -  Automatically trigger an agent wakeup task for devices which
         have been forced to update to quickly update their new DAT
         version within ePO and release it from the quarantine state in
         NAC.

      -  Generate XMC events/alarms for all McAfee end-systems which
         have a DAT version older than X versions to infrom the network
         and ePO admins about devices that are missing regular updates.
         Measures to prevent end-systems to get quarantined can be taken
         based on these alarms.

   -  Benefits:

      -  Easy to deploy even in large environments (need to develop and
         deploy different policies/ACLs/VLANs for each device group)

      -  No need to “touch” each device to rollout 802.1X (of course,
         ePO agents need to be deployed)

      -  Automatic live inventory of your whole network infrastructure
         (not only ePO-managed devices) with even more extra data added
         from ePO (visibility, troubleshooting, support)

      -  More substantial security gains through enforcing restrictive
         policies/ACLs/VLANs based on known MAC address groups and
         blocking unknown devices and keeping the devices up to date in
         terms of their ePO DAT version.

      -  Guest support

-  Good:

   -  NAC: Deploy with MAC authentication only (no 1X, no captive
      portal)

   -  ePO Integration: Only import data on devices from ePO and popluate
      them into the custom fields in NAC (no assessment, no user- or
      devicetype overwrite)

   -  Benefits:

      -  Very easy to roll out even in large environments

      -  No need to “touch” each device to rollout 802.1X (of course,
         ePO agents need to be deployed)

      -  Automatic live inventory of your whole network infrastructure
         (not only ePO-managed devices) with extra data added from ePO
         (visibility, troubleshooting, support)

      -  Only minor security gains


******************************
Installation and Configuration
******************************

First confirm that Extreme Management Center, Extreme Control (NAC) and
McAfee ePO are installed. Deploy the NAC solution on all LAN switches
and WLAN SSIDs where you want to authenticate and authorize end-systems
and integrate their data with ePO. Also deploy the ePO agents to those
devices which are authenticated via NAC. If you want to use the
functionality to trigger client update tasks in ePO to update devices
which have an out-of-date agent and DAT version, ensure that such a task
is defined and functional in ePO. Refer to the ePO configuration guide
for this.

If you are running an Extreme Management Center version prior to 7.0 you
will need to update to version 8.1.

Once you successfully installed Connect you can use the Connect tab
within the Management Center to configure the solution using your
browser.

Alternatively you can use the corresponding configuration files which
can be found when login into your Extreme Management server using SSH
and navigating to

/usr/local/Extreme_Networks/XMC/wildfly/standalone/configuration/connect

McAfee ePO Extension
====================

In order for Connect to retrieve device data from ePO a vendor specific
server extension must be installed on the ePO server.

First download the extension from the Extreme Management Center server
using your browser from this link (alter the link to use your XMC’s IP
address or hostname):

This should download a zip file named
*ExtremeNetworks-McAfee_ePO_Extension.zip*. Login to your McAfee ePO
server as an administrator and navigate to *SoftwareExtensions*. Use the
button at the top of the page to add the extension you just downloaded
from XMC. Once installed, you should see the custom third-party
extension from Extreme Networks appear on your list of extensions.

Connect Config: Extreme Control
===============================

|image1|

If you don’t want Connect to add all ePO managed devices to an
end-system group in NAC disable the feature to “Add endsystems to
endsystem groups”. Otherwise all device MAC addresses retrived from ePO
will automatically be added to the NAC end-system group which you
configure on the

If you want to automatically import the username for all devices
retrieved from ePO into NAC you need to enable the following option:
“Update Kerberos username for endsystems“. If ePO has no username
associated with a device, then the existing username in NAC will not be
overwritten/deleted. Be aware that the user information retrieved from
ePO is not retrieved in real-time and can therefore not be used for user
authentication and authorization in NAC. Enable this option only if you
do not use any 1X authentication method and no authorization rules based
on user names. The username retrieved from ePO should only be used as
“additional data on an end-system”.

If you want to augment the device type detection within NAC by
retrieving the operating system information from ePO you need to enable
the following option: “Update devicetype for endsystems”. Since ePO uses
its agents to retrieve the operating system information the quality of
the data is usually very high and it is recommended to enable this
option. But be aware that this feature will overwrite any other device
type detection techniques used by NAC (DHCP, portal, etc.).

Connect Config: McAfeeEPO
=========================

|image2|

This config area offers the most important settings for the ePO
configuration and thus its options are outlined in more detail here:

-  **Username**: Username used to connect to the ePO API

-  **Password:** Password used to connect to the ePO API

-  **Server IP:** IP Address of the ePO API

-  **Server Port:** TCP port of the ePO API. Default: 8443

-  **Poll interval in seconds:** The time (in seconds) the module will
   wait after each run. For example, if you want to run the
   synchronization once per hour you can configure ‘3600’ here.

-  **Module loglevel:** The module’s loglevel setting (DEBUG, INFO,
   WARN, ERROR, FATAL). Default: ERROR

-  **Module enabled:** En-/Disables the module. You will need to set
   this to “true”.

-  **Update local data from remote service:** If this is set to true,
   data from the remote service will be used to update the internal
   end-system table. It is recommended to set this option to “true”. You
   will also need to set this to “true” if you want to populate the
   username and device type from McAfee in NAC (see additional options
   below). Default: true

-  **Default end-system group for all devices from McAfee ePO:** The
   default end-system group name where we assign all McAfee devices to
   in NAC. If you don't want end-systems from McAfee to be assigned to
   this default group, configure a group name which doesn't exist in
   NAC. Default: GroupNameWhichDoesNotExistInNac

-  **Enable Data Persistence:** Enabling this option will force the
   module to store end-system custom field and group membership data
   into a file after each cycle. If this option is disabled, the module
   will forget all data after a service restart, but in order to clean
   already existing data, the corresponding .dat files have to be
   deleted. Default: true

-  **Custom field to use:** The number of the custom data field for each
   end-system to store the data retrieved from ePO. Available values
   are: 1,2,3 or 4. Default: 1

-  **Format of the incoming data for devices from McAfee ePO:** Format
   of the data that gets stored in the custom data field. You can chose
   and combine any of the available variables: ipAddress, macAddress,
   osType, osServicePackVersion, nodeName, userName, datVersion,
   lastUpdate, installedProducts, isEndPointSecurityInstalled,
   amCoreContentComplianceStatus. But be aware that ePO might update the
   “lastUpdate” value for each device very regularly and Connect is
   calling XMC’s API to refresh that value in all end-systems custom
   fields. Depeding on your poll interval this might put a lot of stress
   onto the XMC server and it is thus recommended to *\_NOT\_* use this
   variable here. It should only be used if the poll interval is very
   low (like once per day) and the number of end-systems isn’t too high
   (below 1000). Dfault: NodeName=#nodeName#; OS=#osType#
   (#osServicePackVersion#); User=#userName#; DAT Version=#datVersion#,
   AMCoreCompliance=#amCoreContentComplianceStatus#,
   installedProducts=#installedProducts#

-  **End-system group for decommissioned devices:** The default
   end-system group for devices which existed in ePO but have been
   deleted. If you want to explicitly identifiy those devices and even
   authorize them differently (since they are no longer managed by ePO
   anymore and that could pose a threat) you can configure the group
   they should automatically be moved to here and enable the
   corresponding feature below. Make sure you manually create this
   end-system group in NAC.

-  **Remove device from other groups on decommission:** Enable this to
   move devices which have been deleted from ePO to the NAC end-system
   group configured by the corresponding option above. If disabled,
   devices won't be automatically move to this group but rather stay
   with their existing group membership(s). Default: false

-  **Delete custom data in XMC for decommissioned devices:** If a device
   is deleted in ePO the end-system's custom data field in XMC will be
   cleared as well. On the one hand this will keep your data clean in
   NAC but on the other hand it might often be helpful to still see the
   (old) ePO data for those end-systems which have once been managed by
   ePO. Default: false

-  **Overwrite the existing username with the one acquired from McAfee
   ePO:** If set to "true" the username for devices retrieved from ePO
   will overwrite the username which is already in NAC. If no username
   could be retrieved from ePO for a given end-system, then no change is
   performed in NAC. Be aware that this might mess up existing NAC
   processes if you are already retrieving and using the username
   through some other mechanism like 802.1X or Kerberos snooping -->
   this will be overwritten! Default: false

-  **Overwrite the existing device type for devices with the one
   acquired from McAfee EPO:** If set to "true" the device type
   (operating system) retrieved from ePO will overwrite the device type
   which is already in NAC. If no operating system could be retrieved
   from ePO for a given end-system, then no change is performed in NAC.
   Be aware that this might mess up existing NAC processes if you are
   already retrieving and using the device type through some other
   mechanism like DHCP snooping --> this will be overwritten! But in
   most cases this feature should improve your current method (at least
   for end-systems managed by McAfee EPO) since the quality of the
   information retrieved from ePO is usually very good. Default: false

-  **Max DAT version difference between ePO and client before triggering
   client update task:** For example: If set to "2" and the difference
   between the DAT version on ePO's master catalog and the client's DAT
   version is at least 2 then a client update task is automatically
   triggered. This task is executed by ePO Connect cannot guarantee that
   the task will be executed successfully but if, it should update the
   client's DAT file. Setting this value to 0 will disable this feature.
   Default: 1

-  **Max DAT version difference between ePO and client before generating
   a XMC event:** For example: If set to "4" and the difference between
   the DAT version on ePO's master catalog and the client's DAT version
   is at least 4 then we will generate a new XMC event. The event will
   appear in OneView's "Alarms and Events" tab with event type "Console"
   and category “OneFabricConnect". This feature can be used to create
   XMC alarms based on these events. These alarms could be configured to
   alarm the via Email or trigger other mechanisms. Setting this value
   to 0 will disable this feature. Default: 4.

-  **Max DAT version difference between ePO and client before
   quarantining client via NAC:** For example: If set to "7" and the
   difference between the DAT version on ePO's master catalog and the
   client's DAT version is at least 7 then the value for the
   corresponding assessment test result will be set to 10 and “HIGH”.
   You can use your NAC assessment configuration to automatically push
   those end-systems to a quarantine role if required. Setting this
   value to 0 will disable this feature. Default: 0

-  **Name of the ePO client task that Connect uses to trigger a DAT
   version update for individual devices:** Use the exact name as
   defined in ePO. If you haven't done so far, define a client task in
   ePO that will update a client's DAT file (and maybe even more like
   the agent version, etc.). It will also find any client tasks where
   the configured name is part of – so make sure the provided name is
   unambiguous. Default: Update Agent

-  **Time before client update task is aborted by EPO:** Number of
   minutes after which the EPO server should abort the client update
   task. This value is sent to the EPO server when running the
   "clienttask.run" web service call as an addtional parameter
   ("abortAfterMinutes"). Setting this value to 0 disables this feature
   - the parameter won't be used when making the web service call.
   Default: 10 minutes.

-  **Max number of client update tasks triggered per client per day:**
   To avoid triggering too many EPO client update tasks you can set this
   limit to a non-zero value. We will stop triggering EPO client update
   tasks after the configured maximum number of retries has been reached
   for the current day. As soon as the next day starts (first run after
   midnight), the count of retries per MAC address is automatically
   reset to zero and client update tasks will be triggered again as long
   as the device is still out of date (see
   dat_file_max_difference_before_trigger_update_task) or the maximum
   for that day has been reached again. Setting this value to 0 disables
   this feature the code will trigger a client update task on each cycle
   as long as the device is out of date - no matter how may
   cycles/triggers per day. Default: 1 update task per client per day

-  **Max number of XMC events generated per client per day:** To avoid
   generating too many XMC events you can set this limit to a non-zero
   value. We will stop generating XMC events after the configured
   maximum number of retries has been reached for the current day. As
   soon as the next day starts (first run after midnight), the count of
   retries per MAC address is automatically reset to zero and XMC events
   will be generated again as long as the device is still out of date
   (see dat_file_max_difference_before_generating_XMC_event) or the
   maximum for that day has been reached again. Setting this value to 0
   disables this feature the code will generate a XMC event on each
   cycle as long as the device is out of date - no matter how may
   cycles/triggers per day. Default: 1 event per day

-  **Enable Assessment:** If this is set to true, assessment data for
   all devices managed by ePO will be made available to the assessment
   adapter. The data will be updated on each cycle. So if, for example,
   the McAfeeEPOHandler is configured to run every hour and the DAT
   version of a device is running out-of-date it will take max. up to an
   hour to populate this fact within NAC’s assessment process. Default:
   false

-  **Request an immediate re-assessment of an end-system if its
   DEVICEOUTOFDATE value changed:** If this is set to true, a
   re-assessment of each end-system where its DEVICEOUTOFDATE value
   changed (either from "true" to "false" or the other way round) will
   be requested from NAC. This will ensure that if, for example, an
   end-system has been pushed to Quarantine since its DAT file version
   was out-of-date but now it has updated the DAT version, it will
   immediately be re-assessed and authorized properly. If this feature
   is disabled, it might take hours/days for the end-system to update
   its NAC policy/authorization depending on the NAC assessment
   configuration for this end-system. This feature is only used if the
   assessment feature is also enabled. Default: true

-  **Use XAPI to trigger a reauth and thus also a re-assessment of an
   end-system:** If this is set to true, a re-assessment of an
   end-system will not be performed via a XMC web service call but
   rather executed directly on the access switch of the end-system. This
   will be executed via XAPI so "enable web http(s)" needs to be
   configured on each XOS switch. This will execute the command 'clear
   netlogin state mac-address' with the MAC of the end-system to
   immediately trigger a re-auth. The re-auth then triggers a
   re-assessment of the end-system which should then immediately change
   its authorization state from ACCEPT to QUARANTINE or vise versa. This
   feature is only used if the reassess_endsystem feature is also
   enabled.

-  **Use HTTPS for XAPI calls:** Enable this to use HTTPS instead of
   HTTP for any XAPI communication with all XOS switches. If enabled,
   you will also need to install the SSH mod on all XOS switches and
   configure "enabled web https". This option is only used if the
   reauthenticate_endsystem_using_xapi feature is also enabled.

-  **Username to connect to any XOS switch if no CLI credentials are
   provided within XMC:** If the feature
   reauthenticate_endsystem_using_xapi is enabled, the solution will
   need to authenticate on all XOS switches to perform re-authentication
   of end-systems. It will try to retrieve the corresponding username
   and password from the configured CLI credentials from XMC but if
   there aren't any for a particular switch, then this default value
   will be used

-  **Password to connect to any XOS switch if no CLI credentials are
   provided within XMC:** If the feature
   reauthenticate_endsystem_using_xapi is enabled, the solution will
   need to authenticate on all XOS switches to perform re-authentication
   of end-systems. It will try to retrieve the corresponding username
   and password from the configured CLI credentials from XMC but if
   there aren't any for a particular switch, then this default value
   will be used.

-  **Name of the ePO client task that Connect uses to trigger an agent
   wake up:** Use the exact name as defined in ePO. If you haven't done
   so far, define a client task in ePO that will wake up a client's
   agent. This is required if you want Connect to wake up the agent on
   quarantined end-systems for which a client update task has been
   triggered. By default, ePO agents only report their DAT version to
   the ePO server once per hour. Therefore, Connect will only realize
   that an end-system has updated to the latest DAT Version after quite
   a long time and thus that end-system might be quarantined for quite a
   long time. Sending the latest DAT version to the ePO server through
   an agent wake up task will improve the behavior and get end-systems
   out of their quarantine state quicker

-  **Time before the agent wake up client task is triggered after a
   quarantine event and update task trigger:** In case an end-system was
   quarantined by NAC the code is triggering an ePO client update task.
   This task will try to update the DAT version on the end-system
   through the ePO agent. This process might take a few minutes. After a
   successful update, the ePO agent is not immediately reporting the
   current client DAT version back to the ePO server - it will only
   report this using its standard poll interval which is typically set
   to run once per hour. So in a worst case scenario, an end-system gets
   quarantined, we trigger a client update and then it takes a full hour
   after the agent reports the updated DAT version and Connect can read
   that version and then move that end-system out of quarantine! To
   improve the behavior and shorten the time that end-systems spend in
   quarantine you can use this parameter which will trigger a client
   task on the ePO server that wakes up the corresponding agent x
   seconds after we have triggered the client update task. Setting this
   value to 0 disables this feature. Default: 0.

Install Assessment Adapter (launchAS.sh)
========================================

If you want to use the assessment feature to quarantine McAfee ePO
managed end-systems through Extreme Control you will need to start the
Extreme Control assessment adapter. The adapter is implemented using
this script – which has a different location depending on the XMC
version used.

XMC Version 8.1 and previous 
----------------------------

This version uses a folder called “Connect.war” which contains the
launchAS.sh script in the following sub-folder.

Linux:

*<Extreme Management
CenterRootdir>/wildfly/standalone/deployments/Connect.ear/assessment/launchAS.sh*

Windows:

*<Extreme Management
CenterRootdir>\wildfly\standalone\deployments\Connect.war\assessment\launchAS.cmd*

You will first need to set the executable bit on that script in a Linux
environment to make the script executable:

*cd
/usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.war/assessment/*

XMC Version 8.2
---------------

This version uses a folder called “Connect.ear” which consists of
multiple sub-folder and war files. You first need to unzip the
connect-web.war file to be able to reach the launchAS.sh script
properly. Go into the Connect.ear folder and perform these two steps

*mv connect-web.war connect-web.war.zip*

*unzip connect-web.war.zip -d connect-web.war*

Now you can change into the corresponding folder which holds the script:

*cd connect-web.war/assessment/*

Run the Assessment Adapter
--------------------------

Ensure the script is executable:

*chmod +x launchAS.sh*

You can then try to start the script manually just to ensure it works:

*./launchAS.sh*

If it worked, you will see a very long line of text showing the startup
of the Java Virtual Machine including all its Java libraries.

Then hit CTR-C to stop the script.

To properly start the script as a daemon process running in the
background at all times, edit the */etc/rc.local* file and add these two
lines just before the last line (exit 0).

**XMC Version 8.1 and previous:**

*cd
/usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.war/assessment/*

*nohup ./launchAS.sh >
/usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.war/assessment/launchAS-startup.log
2>&1 &*

**XMC Version 8.2 and following:**

*cd
/usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.ear/connect-web/assessment/*

*nohup ./launchAS.sh >
/usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.
ear/connect-web/assessment/launchAS-startup.log 2>&1 &*

The first line will just change into the correct directory where the
launchAS.sh script is located. The second line will execute that script
using the “nohup” signal which tells Linux to disconnect the process
that started the script from the process running it (send to
background). It also redirects any startup output to the file

*/usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.war/assessment/launchAS-startup.log*

*Or*

*/usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.ear/connect-web/assessment/launchAS-startup.log*

which can be verified later to ensure proper startup.

Now manually start the script using

*service rc.local start*

To verify that the script was started and is running, you should

-  run this command and make sure there is exactly one process that runs
   this script:

   *ps ax \| grep launchAS.sh*

-  Ignore output lines like “grep --color=auto launchAS.sh”

-  run this command and make sure there is exactly one process that runs
   the JVM:

   *ps ax \| grep 8448*

-  The Java Virtual Machine will start the service on port 8448 by
   default and you should see a very long text output as a result of
   this command

-  run this command and make sure that it shows exactly one line for
   port 8448:

   *netstat -an \| grep 8448*

-  Check the start-up log file for any errors (see filename above)

-  Check the assessment adapter’s log file for any warnings or errors:

   | /usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.war/assessment/logs/assessment.log
   | or
   | /usr/local/Extreme_Networks/NetSight/wildfly/standalone/deployments/Connect.ear/connect-web/assessment/logs/assessment.log

If starting the adapter was successful, please reboot the XMC server and
verify that the service has been started automatically (using the same
verification steps as above).

Setup Assessment in Extreme Control
===================================

You must create the assessment adapter as a valid assessment server
before it can be used in NAC Manager.

1. From the assessment configuration, select **Assessment Servers** and
   click **Add** to add a new assessment server.

|image3|

2. In the Edit Assessment Server dialog, provide the required data.

|image4|

-  Assessment Server IP: IP address of the XMC server.

-  Assessment Server Name: a Name for easily identify our server.

-  Assessment Server Port: if launched with the launchAS commands, the
      adapter script (launchAS.sh) runs on server 8448.

-  Assessment Server Type: FusionAssessmentAgent

-  Max Concurrent Scans: leave empty. This can be used afterwards to
      increase the capacity of the server. By default the server allows
      10 concurrent scans.

   To use this server for assessment purposes, it must be in an
   assessment pool used by an assessment configuration.

..

   Within the assessment configuration you will need to configure a new
   Test Set that uses the new server pool and also uses the
   “FusionAssessmentAgent” type:

|image5|

3. Ensure that you enable **Use Quarantine Policy** in the corresponding
   NAC profile and that the corresponding policy on the WLAN controller
   has a redirect configured within that policy that points to the NAC
   captive portal (external captive portal).

|image6|

4. In the NAC configuration, enable **Assisted Remediation** for NAC to
   display the remediation/self-help page.

|image7|

5. Customize your remediation portal.

   For example, you can add a remediation link to the portal that allows
   users to understand why they have been quarantined and what to do
   about it.

|image8|

6. Define the Custom Remediation Actions to improve the user experience
      with the help texts on the remediation page.

*************
Program Logic
*************

This sections describes how the ePO integration operates.

Overview
========

There are mainly two processes that make up the McAfee ePO integration
within Connect:

1. **Update Cycle** 
      This is the full update process Connect executes over and over
      again as long as the McAfee ePO module is enabled. It will execute
      the process and once it is finished it will wait for the
      configured amount of seconds (poll interval) before executing it
      again. This process performs most major tasks like:

   a. Retrieve the Master DAT version and all require tasks (clien
         update, agent wakeup) from ePO

   b. Retrieve the latestat status on all ePO managed devices

   c. Update the internal assessment storage with latest ePO client data

   d. Force out-of-date end-systems into quarantine immediately

   e. Trigger client update tasks via ePO

   f. Generate XMC events for out-of-date end-systems

2. **Quarantine Event Handling** 
      This is a parallel process that gets triggered evertime an
      end-system gets quarantined by NAC. It will:

   g. Immediately trigger a client update task for any end-system that
         gets quarantined

   h. Trigger an agent wakeup task after waiting some time for the
         client update taks to finish

..

   Both processes are described in more detail below.

   The most important and critical use case for the McAfee integration
   is the situation where an end-system is quarantined. The following
   section walks us through this use case and lists a few important
   considerations / facts:

|image9|

1. When NAC performs an assessment on an ePO end-system it will retrieve
      the current assessment results from Connect which retrieved this
      data during its last update cycle

2. If an end-system has a “high risk” result since its ePO DAT version
      is out-of-date NAC will quarantine the end-system

3. Thise NAC event/action will trigger an action in Connect that then
      triggers an ePO client update task

4. Now ePO starts updating the client. Connect doesn’t know how long
      this process takes and doesn’t get informed by ePO when this
      processes finishes

5. Due to that suboptimal situation Connect can be configured to sleep
      for X seconds and thus wait for the ePO client update task to
      finish. Our testings have shown that a 6 minute timeframe usually
      works and provides enough time for ePO to update the client

6. Since the ePO client update task is not updating the ePO server with
      the new client DAT version, Connect is now triggering another
      task: the agent wakeup task will force the agent on the client to
      communicate with the ePO server and update its DAT version.
      **McAfee provides no feedback when this task is finished or
      whether it was successful.** This is the last action taking during
      the *quarantine event handling* process within Connect.

7. | Depending on the configured poll interval of the McAfee Connect
        module and when it was last running the next Connect *update
        cycle process* will start. If you configured, for example, a 6
        minute poll interval on the Connect McAfee ePO module, it could
        take up to 6 minutes after the Connect *quarantine event
        handling* process triggered the agent wakeup task before the
        Connect *update cycle* process is running again.
      | Connect will retrieve the latest status of all ePO devices.

8. When Connect recognizes that the status of an end-system changed from
      *quarantine* to *accept* then it needs to trigger a
      re-authentication and re-assessment via NAC and/or directly on the
      access switch. NAC will immediately perform a new assessment which
      now retrieves the “low risk” result from Connect and thus moves
      the quarantined end-system to its “normal” VLAN/policy.

..  warning::
    The whole process starting with NAC quarantining and end-system and ending with end-system being fully updated and back in its “normal” policy/VLAN can take multiple minutes. Realistic values seen in tests: 12 minutes. This process time is mainly made up of:

    1. Time it takes for ePO to update the client – recommended wait time to configure in Connect: 6 minutes. This could be lowered but if ePO hasn’t updated the client after those x minutes, Connect will trigger the agent wakeup task too early and it might take up to another hour until the client will actually be updated in ePO and Connect/NAC can undo the quarantine state!

    2. Time it takes for ePO to wakeup the agent and update its client status. Connect is currently not waiting for this to finish. Connect will simply read the end-system’s status during its next update cycle.

    So if, for example, the agent wakeup task is triggered at 15:00 and the next Connect update cycle runs 2 minutes later at 15:02 ePO might not yet have update the end-system’s state so Connect won’t push the end-system out of quarantine yet as the ePO server still shows the out-of-date DAT version! Only on the next Connect update cycle (assuming a poll interval of 6 minutes) at 15:08 will Connect realize that the client was updated by ePO and push it out of quarantine. Using this example: Best case, the full process will take a bit more than 6 minutes, worst case it might take more than 12 minutes.


Update Cycle
============

All operations described in this section take place during each cycle of
the McAfee ePO handler. If, for example, you have configured the
handler’s poll interval to be 360 seconds, this module will wait for 360
seconds after all operations described below have been performed and
then run them all over again.

Since there are many steps in this process, they are cut to smaller
sections to make the various screenshots usable and clear.

Initial Setup
-------------

This is the first phase and describes the first steps taken during each
cycle. These steps are important to setup some of the basic data for the
following operations. The image shows the Extreme Management Center /
Control Center (NAC) at the top, the McAfee ePO handler in the middle
and the McAfee ePO server at the bottom.

|image10|

1. **Read the config** file and update any changes found [not shown on
      the image]. This means, that almost all settings specific to the
      ePO integration can be modified without the need to restart the
      XMC server. It also means that any config change will only take
      effect on the next cycle/iteration.

2. **Retrieve latest DAT version from ePO’s master catalog**: usually
      ePO is configured to update its master DAT version from McAfee
      (Intel) regularly – often McAfee (Intel) provides a new major DAT
      version every day. The ePO handler is retrieving the latest DAT
      version so it can use that master version lateron to compare this
      to the end-systems’ DAT version and verify if a device is
      out-of-date or not.

3. **Retrieve information on two required client tasks**
      The first one will be used to trigger ePO to update a
      client’s DAT version immediately.
      The second one will be used to trigger a client wakeup action.
      This is needed to force the ePO agent to update its state
      (including its DAT version) with the ePO server.
      That step uses the client task names given in the config file to
      find the client task within ePO that matches this name and get
      more data on it. This step is required as the API requires to
      provide a task ID and product name when triggering a client
      update task. If Connect finds more than exactly one client task
      that matches the given name from within the config file we will
      log an error and not be able to trigger client update or agent
      wakeup tasks later on!

4. **Is this the first cycle of a new day (=first cycle after midnight)?** 
      If so, clear two lists: the first tracks the number
      of generated XMC events per day. The second one tracks the number
      of triggered ePO client update tasks per day.

5. | **Remove old entries from list of triggered client update task
        timestamps.** This list holds one entry for each MAC address for
        which Connect triggered an ePO client update task recently. The
        time an entry lives on that list can be modified using config
        property
      | *“Time before client update task is aborted by EPO”*

6. **Retrieve switches from XMC**. In case the feature to use XAPI to
      re-authenticate end-systems is enabled, Connect will need to get
      information on all XOS switches from XMC to know how to
      authenticate (user credentials) on each switch when it has to
      execute the “clear netlogin…” command to re-auth end-systems.

Retrive ePO Devices and start Assessment
----------------------------------------

In the second phase, Connect is retrieving the latest updates on the ePO
devices and starts the assessment.

|image11|

7. **Retrieve ePO devices.** Connect is using the API provided by the
      McAfee ePO server to retrive data on all devices managed through
      ePO. It will store this data on an internal list. Stored data: MAC
      address, IP address, node name, user, operating system, last
      updated, DAT version.

At this stage, the ePO Connect module is starting to iterate through all
retrieve ePO devices and performing all following operations on each
device.

Not on the image:

-  Set the end-system group to assign in NAC. At this stage, all
   end-systems found in ePO are assigned to the same default end-system
   group in NAC. If you don’t want this to occur (because you might want
   to organize your devices in other groups independent from ePO) you
   need to configure an end-system group name which doesn’t exist in
   NAC.

-  If the feature to import data from ePO is enabled:

   -  Update the custom field with the latest data retrieved from ePO
      (if there are any changes). Be aware to not include the
      “LastUpdate” variable within the configuration for the custom
      field syntax in a larger environment with many devices managed by
      ePO – otherwise this will update the custom data on each cycle and
      put a lot of stress on the XMC server.

   -  Update the username retrieved from ePO within NAC’s username
      field. You need to explicitly enable this feature in addition to
      the import data feature.

   -  Update the device type retrieved from ePO within NAC’s device type
      fields. You need to explicitly enable this feature in addition to
      the import data feature.

8. Store latest device data as assessment data. This is only performed
      if the assessment feature is enabled. Any update of those “health
      results” will only be visible in OneView after the next NAC
      assessment has been performed on an end-system. This data from ePO
      will be stored in the internal assessment datastore:

   a. Last update time. Corresponding assessment plugin name “LASTSEEN”

   b. DAT version. Corresponding assessment plugin name “OSVERSION”

   c. | Out-of-date state (true or false). Corresponding assessment
           plugin name “DEVICEOUTOFDATE”. If the feature to quarantine
           end-systems in case their DAT version is older than X days is
           enabled, a value of “true”, a score of 10.0 and risk of
           “HIGH” will be assigned to this assessment test. This can be
           used by the standard NAC process to actually quarantine the
           end-system based on the high risk value.
         | In case the operating system retrieved from ePO for the
           end-system contains the word “Server” the score will only be
           set to 6 and thus these server systems won’t be quarantined
           by NAC by default! This is due to the fact that servers are
           often run as virtual machines and McAfee uses a different
           technique to manage DAT versions on those VMs so those agents
           might report a DAT version of 0 (which would always mean
           “out-of-date”) but they are in fact managed and up to date.

Quarantine End-System
---------------------

In this third phase, Connect is evaluating whether it has to quarantine
an end-system and then execute the necessary actions.

|image12|

9. The whole quarantine process will only be executed for an end-system
      in case the following requirements are met:

   d. The assessment feature must be enabled

   e. The end-system’s DAT version must be out-of-date

   f. The out-of-date state must have changed in this cycle. That means
         that during the last cycle, the same end-system was not yet
         out-of-date but now it is now during the current cycle. This is
         evaluated to avoid having to quarantine an end-system again and
         again on each cycle as long as its DAT version is out-of-date.

   g. The feature to reassess an end-system must be enabled. If not
         enabled, the end-system will still be flagged with the
         quarantine state internally but NAC won’t actually push it to
         the quarantine state (and policy/VLAN) unless the next
         re-auth/re-assessment occurred for this end-system. This
         feature forces the immediate enforcement of the quarantine
         state for out-of-date end-systems.

8.1. If the feature to re-authenticate end-systems using XAPI is
disabled, Connect will trigger NAC to force a re-auth using its standard
mechanisms (see 8.2). Some XOS switches are not supported by those
mechanisms. In these cases, this feature should be enabled. If enabled,
Connect will retrieve the corresponding end-system from NAC and augment
it with the switch data where this end-system is connected to.

8.2. Connect is triggering NAC to re-auth and re-assess the end-system.
This might not work on some XOS switches (see 8.1.). Even in that case,
this step is important since it will tell NAC to assess this end-system
as soon as it re-authenticates the next time.

8.3. Trigger a re-authentication of this end-system on its access
switch using the XAPI and the following CLI command: 
*clear netlogin state mac-address <MAC>* 
This feature is only meant for supporting older XOS switches which
cannot re-authenticate end-system based on standard features provided
by NAC (SNMP MIB or COA).
This will force the switch to send a new RADIUS authentication request
towards NAC. Since Connect told NAC to reassess the end-system on the
next occurance of a re-authentiation (see 8.2.), NAC will now assess
the end-system. NAC is configured to assess the end-system via the
“FusionAssessmentAdapter” (=launchAS.sh script/assessment adatper)
which in turn asks the internal Connect assessment storage. Within
that storage, this end-system has been marked with a *high risk* value
for the *DeviceOutOfDate* test and thus NAC will quarantine the
end-system. Quarantining an end-system could mean: push the end-system
to the Quarantine VLAN or Policy or similar – depending on the NAC
Profile configuration.

8.4. Step 8.5 is only executed if the following requirements are met:

   - The end-system state has changed from Accept to Quarantine this
     ensures that Connect is not triggering a client update task during
     each cycle as long as the end-system stays in the Quarantine state

   - Connect doesn’t have a running ePO client update task already. This
     check tries to find an entry for the end-system MAC address within
     the list of triggered client update task timestamps. This list holds
     one entry for each MAC address for which Connect triggered an ePO
     client update task recently. The time an entry lives on that list can
     be modified using config property 
     *"Time before client update task is aborted by EPO"*

8.5. Trigger client update task: calls the McAfee ePO API task that
triggers a client update immediately. Also adds +1 to the number of
triggered ePO client tasks for that end-system for that day and
memorizes when this task was started (so Connect doesn’t start multiple
client update tasks simultaneously)

ePO Client Update
-----------------

In this forth phase, Connect is evaluating whether it has to trigger a
client update task via ePO’s API.

|image13|

10. A client update task will only be triggered in this phase if all of
       the following requirements are met:

    h. The feature to trigger ePO client update tasks is enabled

    i. The DAT version is older than the allowed difference

    j. The end-system is currently in either “ACCEPT” or “QUARANTINE”
          state in NAC (this avoids triggering client update tasks for
          end-systems which are not connected/reachable)

    k. If there is a configured limit of the number of client update
          tasks to trigger per day: the limit has not been reached for
          the end-system for today

    l. If Connect hasn’t recently triggered a client update task for
          that end-system which is still on the internal list of running
          tasks. See config property “Time before client update task is
          aborted by EPO”

If all criteria are met, Connect will trigger a client update task via
the ePO API. If the end-system has just transitioned to the Quarantine
state (see 8.) the previous process might have already triggered a
client update task so this process won’t need to anymore.

Generate XMC Event
------------------

In this fifth phase, Connect is evaluating whether it has to generate a
XMC event. The purpose of generating XMC events for ePO devices with
out-of-date DAT versions is general reporting, audit and the ability to
immediately inform an administrator about this situation with an email
action configured for this event type.

|image14|

11. The XMC event will only be generated if all these requirements are
       met:

    -  The feature to generate a XMC event is enabled

    -  The end-system’s DAT version is older than the configured maximum
          value

    1. Retrieve the current state of the end-system from NAC. The
          end-system must be in state “ACCEPT” or “QUARANTINE” to
          generate a XMC event. This avoids generating events for
          end-systems that are not connected to the network
          (misconfigured ePO devices, employees who are on long
          vacations, etc.)

    2. If the maximum number of XMC events to trigger per day for the
          end-system hasn’t been reached generate an event in XMC.
          Admins can see those events in OneView under the “Console”
          type of events. Admins can use the alarm configuration to
          create a new alarm configuration that triggers based on these
          auto-generated events and, for example, sends and email to the
          admin group whenever such events occur.

Cleanup
-------

In this sixth and final phase, Connect is cleaning some data and
potentially moving ePO devices to a configured decommission NAC group.

|image15|

12. The first cleanup task is to remove end-systems from Connect’s
       internal list if they are no longer managed by ePO.

13. Connect also deletes any internal assessment data for those deleted
       end-systems. This is not deleting any assessment (health) results
       in NAC! So result of previous assessments will still be
       maintained in NAC but Connect will forget all assessment data on
       those deleted devices.

14. If there is a configured NAC end-system group to move decommissioned
       devices to, Connect will push all deleted MAC address to that
       end-system group. Administrators could use this list to track
       deleted devices and also block them from ever entering the
       network again (for example: when stolen).

15. If the feature to clear the end-system’s custom data is enabled,
       Connect will delete any custom data for that end-system in NAC.

..

   This ends Connect’s update cycle. Connect’s McAfee ePO module will
   now sleep for the configured “poll interval” timeframe and then
   restart this cycle again.

Quarantine Event Handling
=========================

In parallel to the operations described in the previous section (“Update
Cycle”) the actions described here are performed whenever an end-system
is quarantined by NAC. This is implemented as a multi-thread approach so
the actions taken when an end-system is quarantined can run in parallel
to a regular update cycle.

|image16|

1.  The overall process starts with a regular RADIUS authentication
       triggered by an access switch. The describe use case is
       independent on why the end-system authentcation occurs: it could
       be because an employee connects his/her laptop to the network to
       start working in the morning, moves to a different room and
       reconnects the laptop, a valid authentication session on the
       switch ends for an end-system or a re-auth triggered by Connect
       when it found an end-system needs to be quarantined. NAC will
       first try to authenticate the end-system (MAC) or user (1X).

2.  Then NAC will determine whether it needs to perform an assessment
       for the end-system. NAC is not running an assessment on all
       end-systems any time they authenticate! Depending on the NAC
       configuration, end-system assessment could, for example, be
       limited to max. once a day. So even if an end-system is
       reauthenticated 10 times a day it might still be assessed only
       once that day! The only ways to force an immediate re-assessment
       of an end-system are to manually request it through the NAC
       management GUI or by Connect which marks end-systems for
       immediate reassessment in case they need to be quarantined.

3.  NAC is configured to run an assessment via the so called
       “FusionAssessmentAdapter”. This assessment server config points
       to a local assessment adapter service that needs to be running on
       the XMC appliance. The script to start this service is called
       “launchAS.sh”.

4.  | That adapter service acts as a connector between NAC and Connect’s
         assessment data. All it does is to retrieve the current
         assessment result from Connect for the end-system (by IP) that
         NAC is trying to assess. It is important to understand that
         this assessment process is **not** retrieving the latest status
         of the device from McAfee ePO! It is simply retrieving the
         current assessment data from Connect’s local storage. That
         storage is only updated with ePO data on each Connect cycle.
       | Exampe: if you have configured the Connect McAfee module to run
         every 6 minutes (360 seconds poll interval) then the assessment
         data that NAC retrieves through this process can be anywhere
         **between 0 and 6 minutes old!**

5.  Connect is reading its local assessment storage list and returns the
       health results (last seen time, dat version, DAT out-of-date
       state) to the assessment adapter script which then returns it to
       NAC.

6.  NAC is evaluating the assessment test results. If one of them has a
       score of 7.0 or higher, NAC will quarantine the end-system. The
       Connect McAfee module will automatically set the score for the
       “DAT out-of-date state” test to a value of 10 and thus forces NAC
       to quarantine the end-system. The only exception is if the McAfee
       device is a server type system – then the score will only be set
       to 6.0 and thus NAC won’t quarantine servers.

7.  In order to quarantine the end-system, NAC will send a corresponding
       RADIUS response to the access switch. This could contain a
       Quarantine VLAN, a Quarantine policy or other RADIUS attributes
       that will tell the switch to quarantine the end-system. This
       depends on the feature set of the switch infrastructure and your
       NAC config.

8.  Since NAC quarantined the end-system, this is triggering a
       quarantine event through the NAC notification engine. Connect is
       configured to listen to all NAC events and can trigger additional
       tasks when certain events occur.

9.  This process will only trigger on quarantine events and ignore all
       other types of NAC events. In case of a quarantine event, it will
       try to lookup the MAC address of the quarantined end-system
       within Connect’s McAfee device list. This process will only
       trigger actions for McAfee ePO devices that have been
       quarantined.

10. Beore triggering a new client update task, Connect is validating
       whether it didn’t yet start an ePO client update task and whether
       the limit of max client update tasks per days hasn’t been reached
       yet.

11. Connect is triggering a client update task via the McAfee ePO API
       and updates its internal tracking lists.

12. | Connect doesn’t get any feedback from McAfee whether (and when) a
         client update task succeeded and actually updated the client.
         And even when a client has been updated, McAfee is not
         automatically updating the info on the new end-system’s DAT
         version on its server side. But Connect can only query those
         DAT versions from the server. Since this update (between ePO
         server and ePO agent) seems to only occur once per hour it can
         **take up to one hour** for ePO to recognize the new DAT
         version on an end-system and even longer for Connect to
         retrieve this update. Until then, those end-systems will stay
         in quarantine which often is not acceptable from an operational
         persepective.
       | Connect needs to know as soon as possible if and when an
         end-system could be updated by ePO to get it out of quarantine.
       | To avoid having to wait that long for Connect to be able to
         valide the update DAT version and thus tell NAC to move the
         end-system out of quarantine, administrators can enable this
         feature (see below).

13. After triggering the client update task Connect is waiting for the
       configured amount of time. This is implemented in the same thread
       that was processing the NAC end-system quarantine event and also
       triggered the client update task. So this sleeping thread is only
       responsible for a single end-system.

14. As soon as the wait time is over, Connect is triggering the agent
       wakeup task via the ePO API. Connect assumes that after the wait
       time, the end-system has been successfully updated via ePO and
       now it just needs to force the immediate agent wakeup (sync with
       the ePO server). This will ensure that Connect will read the
       latest client DAT version on its next regular cycle and can then
       push the end-system out of quarantine.

************
Verification
************

This section provides information related to verifying that your
integration is configured properly and performing as expected. In
general, be aware that any data (including assessment data) will only be
updated during the configured update intervals. For example, if you
update only once per day, do not expect any updates within NAC more than
exactly once per day. Also be aware of the fact that any data retrieved
from ePO and any action triggered in direction to XMC are handled by the
Control module, which has its own update interval and needs to pickup
any changes/updates from ePOHandler and push it to XMC. Depending on the
number of changes/actions during one cycle and the number of end-systems
managed, you will need to provide some time before you validate the data
in XMC/OneView.

Data Import to NAC
==================

There are multiple areas to verify whether data on all devices managed
by ePO is imported to NAC. The first option is to use OneView’s
end-system table under the “Identity and Access” tab and display the
custom data field which you have configured for the McAfeeEPOHandler.
You might need to make the corresponding column visible first. If you
enabled the corresponding features you should also see the username
retrieved from ePO and a more detailed Device Type also retrieved from
ePO.

|image17|

Another option is to use the general “Search” tab and search for an
end-system which is managed by ePO. It should find the end-system and
display ePO data as shown below.

|image18|

You can also verify whether all ePO-managed devices have been assigned
to the default end-system group in NAC if you configured an existing
group in NAC and want to use this feature.

Assessment
==========

This section describes the process a devices will follow if it its DAT
file is running out-of-date and the corresponding assessment features
are enabled.

A healthy device did not update to the latest ePO DAT version and is
thus running a DAT version which is older than X versions configured in
the ePO handler config file. Once Connect recognizes the outdated DAT
file it will populate that fact to the assessment adapter and also try
to trigger the corresponding client update script. If NAC triggers an
assessment for this end-system before the device could be updated, it
will recognize that the device is out-of-date and needs to be
quarantined:

|image19|

|image20|

At this stage, the device should have a policy (or VLAN) that doesn’t
allow it to harm other network devices or services but still allows the
ePO server to contact and update it.

Once ePO has successfully updated the device and the next Connect update
cycle ran, the assessment adapter will receive the updated info (from
Connect) that the device is no longer out-of-date. Connect will then
immediately trigger a re-assessment within NAC which will lead to
re-authorizing the device into its proper policy (VLAN) since the new
assessment result showed that the device is compliant and the DAT is not
out-of-date anymore.

|image21|

Handling Deleted ePO Devices
============================

To test this workflow simply remove/delete a device from ePO and wait
for the next Connect synchronization. Then verify that

-  This device’s custom field has been emptied (if this feature has been
   enabled in the config file)

-  This device is now member of the NAC end-system group for
   decommissioned devices (if this feature has been enabled in the
   config file)

-  This device does not appear in the end-system list that is displayed
   at the bottom of the Connect management web site (tab: McAfee ePO)
   this means that the device has been deleted in the internal list as
   well

***************
Troubleshooting
***************

This section describes steps you can take when you experience issues
with the McAfee ePO integration.

1. Issue: I’ve installed and configured Connect and waited for a long
   time but I still don’t see any data coming into any custom field of
   any device, nor do I see assessment results during my scans.

   Things to ensure:

-  Make sure you restarted the XMC server after you’ve successfully
   installed and configured Connect.

-  Make sure you have enabled the feature to import data from ePO within
   the config file/management page.

-  Make sure that the XMC useraccount and password configured for the
   XMC handler are correct and have sufficient access rights to talk to
   the XMC API

-  Make sure that the ePO useraccount and password configured for the
   ePO handler are correct and have sufficient access rights to talk to
   the ePO API

-  Make sure that you can telnet from the XMC server to the ePO server’s
   IP and configured TCP port (so Connect can talk to the API on that IP
   and port)

-  Are you sure that the devices managed by ePO are more or less the
   same as those managed within NAC? Connect will only import and push
   data for those MAC addresses which are matching in ePO and NAC.

2. Issue: I don’t get the DAT version imported correctly in NAC and the
   assessment regarding the DAT out-of-date value isn’t working
   properly.

-  Did you also rollout McAfee VirusScan Enterprise 8.8 to the agents
   running on your devices? Can you verify by using your ePO web
   management and navigate to a device whether ePO shows the correct DAT
   version within the device’s Products section?

-  If a device has just recently been added to ePO it might already be
   managed by ePO but VSE might not yet have been rolled out so there is
   no DAT version to retrieve at this stage. This should update
   automatically lateron.

.. |image1| image:: media/image1.png
   :width: 6.27014in
   :height: 4.15278in
.. |image2| image:: media/image2.png
   :width: 6.27014in
   :height: 3.71042in
.. |image3| image:: media/image3.png
   :width: 6.20764in
   :height: 2.635in
.. |image4| image:: media/image4.png
   :width: 3.3125in
   :height: 2.3159in
.. |image5| image:: media/image5.png
   :width: 3.63208in
   :height: 2.71408in
.. |image6| image:: media/image6.png
   :width: 3.85701in
   :height: 3.11458in
.. |image7| image:: media/image7.png
   :width: 6.20764in
   :height: 2.5074in
.. |image8| image:: media/image8.png
   :width: 6.20764in
   :height: 4.68616in
.. |image9| image:: media/image9.png
   :width: 6.62119in
   :height: 4.144in
.. |image10| image:: media/image10.png
   :width: 6.27014in
   :height: 4.74934in
.. |image11| image:: media/image11.png
   :width: 6.27014in
   :height: 6.02635in
.. |image12| image:: media/image12.png
   :width: 6.73913in
   :height: 5.1747in
.. |image13| image:: media/image13.png
   :width: 5.49995in
   :height: 5.22727in
.. |image14| image:: media/image14.png
   :width: 3.09432in
   :height: 4.84416in
.. |image15| image:: media/image15.png
   :width: 4.44061in
   :height: 4.808in
.. |image16| image:: media/image16.png
   :width: 6.27014in
   :height: 4.25786in
.. |image17| image:: media/image17.png
   :width: 6.26944in
   :height: 1.03472in
.. |image18| image:: media/image18.png
   :width: 6.26944in
   :height: 2.49583in
.. |image19| image:: media/image19.png
   :width: 6.27014in
   :height: 2.33631in
.. |image20| image:: media/image20.png
   :width: 5.39623in
   :height: 4.76025in
.. |image21| image:: media/image21.png
   :width: 6.27014in
   :height: 2.32834in
