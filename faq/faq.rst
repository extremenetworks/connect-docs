###
FAQ
###

This guide lists the frequently asked questions and tries to answer them.

.. contents:: Contents
   :depth: 3

****************************
Installation & Configuration
****************************

**I’m getting a java error while trying to start the installer, what can I do?** 
Mostly, this happens while using an older JRE to execute the
installer. It is best to use the JRE in the Extreme Management Center
Java directory.

**What ports does Extreme Connect use?** 
Upcoming Extreme Connect modules may use additional/different
ports. Currently these ports are used by all modules:
| - 443 (HTTPS)
| - 80 (HTTP)
| - 8443 (HTTPS)
| - any port as configured by the various Adapters

**How do I reset module passwords using the CLI?** 
Extreme Connect does store password in an encrypted format for
security purposes. To reset a password, simply open the configuration
file, change the password and set the “crypt” attribute to “false”. Upon
the next run cycle, the password will be encrypted automatically and the
“crypt” attribute is set to “true”.

**How do I start the installer in CLI mode (no GUI available)?** 
Simply add **–console** at the end of the **java –jar …** command

**Does Extreme Connect use a database?** 
Currently, Extreme Connect does not store any data (except from
configuration) persistently. All information is kept in memory and
cleared when Extreme Management Center (or the JBoss service) is
restarted.

**Which files are modified by Extreme Connect upon installation?** 
These files will be modified (and backed up beforehand) in the Extreme Control directory:
| - ../jboss/server/default/deploy/fusion_jboss.war/\*
| - ../jboss/server/default/conf/fusion/\*
| - ../jboss/server/default/conf/log4j.xml
| - ../appdata/NSJboss.properties
| - ../appdata/System/Shared/ThirdPartyMenu.xml

**I want to change the configuration on the CLI, where can I find it?** 
The configuration files for all modules are stored in
../jboss/server/default/conf/fusion/.
All files use an XML format and must comply with the internal data
model. Be aware that faulty settings may force the module to shut down
or might cause other unpredictable problems. In general it is safer to
use the configuration web page where all data will be stored according
to the data model.

**I changed the configuration of a module, do I need to restart the Extreme Management Center service before the changes will apply?** 
No. The modules constantly check if the configuration files were
modified and will reload them at the next run cycle.

**Is it possible to switch to another language on the configuration page?** 
Not yet, however, it is possible to manually translate all text
information. The web page is dynamically created from the configuration
files. Therefore, all that needs to be done is to translate the <info>
parts in a configuration file to the desired language.


**How are the adapters for SCVMM, Hyper-V and XenDesktop working?**

This are adapters written in Java that will use Windows Powershell
commands to retrieve data von virtual machines (SCVMM) and virtual
desktops (XenDesktop). They are acting as a server in a client-server
relationship with the corresponding Extreme Connect module. During each
interval configure, the corresponding module is acting as a web service
client and calling the web service server (=the adapter) to get
information from that adapter. The communication uses the configured) IP
address, port and pre-shared key. The adapter gathers the requested data
using Powershell commands and then returns that data to the
corresponding module in an encrypted way. The Extreme Connect module is
then populating that information within Extreme Management Center/NAC.


***************
Troubleshooting
***************

**When accessing the Extreme Connect, the Connect tab is missing.**

If the tab is not found, the Extreme Connect plugin was not installed or
an Advanced License is not present. Install or reinstall the Extreme
Connect plugin, update the Extreme Management License to Advance and
restart the services.

**Extreme Management Center is not responding.**

Restart the Extreme Management Center services. Change directory (cd) to
/usr/local/Extreme_Networks/Extreme Management Center/scripts.

**cd /usr/local/Extreme_Networks/Extreme Management Center/scripts**

stop Extreme Management Center service by typing:

**./stopserver.sh**

Wait for the prompt and then start Extreme Management Center service by
typing:

**./startserver.sh**

**How do I restart/reset Extreme Connect?** 
Extreme Connect runs within the JBoss context and can be restarted
by simply restarting the JBoss service (or Extreme Control). If the
Extreme Connect cache needs to be reset, simply shut down the Extreme
Management Center service and delete the \*.dat files under
../jboss/server/default/conf/udcp/ of the Extreme Management Center
installation directory.

**Is there a log file and where do I find it?** 
Extreme Connect logs within the JBoss context of the Extreme
Management Center server. You may find the server.log file either in the
../appdata/logs/ folder or simply by opening the server log from any
Extreme Management Center Client.

**What loglevels are available and how do I change them?**
Every module of Extreme Connect, including the main application
itself have individual loglevel settings in their respective
configuration file. The default level should be ERROR and it is
strongly suggested to keep it at this level, except for
troubleshooting issues. The loglevels are (from least to most
talkative):
| - ERROR
| - WARN
| - INFO
| - DEBUG

**I am getting a lot of errors and would like to turn logging completely off for a certain module.**
In addition to the four loglevels used by all modules, Log4J also
supports the FATAL loglevel which is currently not used by any module
without Extreme Connect. In order to set a module to use this
loglevel, the configuration file has to be edited manually as this
option is not provided on the web page to avoid shutting down logging
by mistake.

**Some modules stop working after some time and report in the log that too many errors happened.**
Each module is monitored by the main Extreme Connect process
regarding errors that happen during each run cycle (i.e.
authentication errors). If a module produces more than 10 failures in
a row, the module will be disabled to prevent any further errors. In
order to restart a module, try to identify the problem source (i.e.
remote server is not responding), remedy it and update the module
configuration file. As soon as the timestamp of the configuration file
is changed, the configuration will be reloaded and the failure counter
is reset to zero until further failures happen. The counter will also
be reset, if at least one successful cycle was completed in the
meantime.

**The logs always note local/remote data storages. What are these?**
Extreme Connect logs are always written from the Extreme Connect
perspective, meaning that **local** means the Extreme Connect service
and **remote** relates to another service contacted (i.e. Extreme
Control, VMware,…). Each module has its own datastore in order to
track changes and update local or remote data. Therefore, if certain
information for an end-system is missing from a specific module, it is
always a good start to look at the datastore and log for that
particular module.

**What happens to a module if an error occurs?**
In most cases, the error is logged and the run cycle for the module
will go on or end, depending on the severity of the error. If an error
should crash a module, a full stack trace will be logged and the module
is terminated until the JBoss service has been restarted. All other
modules are not affected by this and will continue running, even if they
should not receive any further updates from other modules.

**After JBoss has started, I don’t see any data being updated for some minutes. Is there something wrong?**
No, Extreme Connect will first start all modules and wait a bit to
verify that everything is running correctly. After that, the modules
will enter their run cycle and start retrieving data from various
sources. Depending on the delay until the information is retrieved and
the interval times of each module, this might take up to a couple of
minutes.

Extreme Management Center
-------------------------

**How does Extreme Connect communicate with Extreme Management Center?**
Extreme Connect solely uses Extreme Management Center web service
calls to retrieve and/or alter data. There is no direct access from the
module involving the Extreme Management Center database, even though
both usually run on the same server.

**Is it possible to use one instance of Extreme Connect with multiple Extreme Management Center servers?**
No.

**Extreme Connect is supposed to update a custom field in Extreme Management Center NAC manager for each end-system, but I don’t see such a field. How can I make the custom fields visible?**
Simply right-click on any of the end-system table headers in Extreme
Management Center NAC Manager and change the view properties to un-hide
the desired custom fields.

**Where is the configuration page for Extreme Connect located?**
The direct access URL is `https://Extreme Management
Center-IP:8443/fusion_jboss/ <https://NetSight-IP:8443/fusion_jboss/>`__\ *,*
or simply access the page via the Connect tab Extreme Management Center.

**There is an Axis error in the logs about an unknown “html” method error, what’s that about?**
The server responded to a request with a simple HTML page. This is
most likely related to wrong user/password information where the server
displays a login error, but the application is unable to handle this
kind of output. Check the account information for spelling errors and
make sure to add the domain/computer name to the username if Extreme
Management Center is running on a Windows server.


**************
VMware vSphere
**************

**Do I have to create a dedicated user for Extreme Connect to access the vSphere webservice?**
No, but it is recommended to do so as it will allow you to filter
events and tasks more easily within the VMware Client.

**What are the least permission requirements for the webservice user?**
The account should have at least all necessary permissions to:
| - register the Extreme Management Center Plugin Extension
| - write data to VM annotation fields
| - read data from VM configurations (MAC, Network)

**Although Extreme Connect seems to be running fine, I only see “n/a” in the annotation fields and no records via the Extreme Connect plugin. Why is that?**
Most likely, none of the MAC addresses of the VM is listed in the
end-system table of the NAC Manager. Make sure that authentication (at
least MAC Auth) is set up properly on the physical switch and that the
VM is actually sending some traffic.

**How often will Extreme Connect update the information within vSphere (annotations, switches…etc.)?**
Extreme Connect will check if the current remote data differs from
its local. If so, it will update all data that is different on the
remote service. This is especially true for the annotation field and it
is generally recommended not to use variables like LastSeenTime in the
annotation text, which will change very frequently and have a lot of
updates as a result.

**Is there any way to get rid of the event/task logs for every update that Extreme Connect performs within vSphere?**
No. This functionality is handled by vSphere itself and Extreme
Connect has no means to stop it. vSphere offers a filtering mechanism
that can be used to limit the information shown and help to find
specific data more efficiently.

**How does Extreme Connect determine the name of the end-system group that a VM MAC address should be added to?**
Extreme Connect retrieves the name of the virtual
network/portgroup in its default configuration and uses the part
before the first underscore as the end-system group name. This
corresponds to the naming convention used if Extreme Connect is
automatically creating portgroups from end-system groups. The format
used there is always:
| *endSystemGroup*\ \_\ *virtualSwitchName*
The reason for this is the requirement within vSphere that two
portgroups on the same host may not share the same name. Therefore,
the (d)vSwitch name is appended to the end-system group name with an
underscore. This also ensures that vMotion is possible for VMs on two
hosts which also require that both portgroups on those hosts have the
same name.

**Is it possible to let Extreme Connect create portgroups automatically, but to let the VM administrator handle VLAN configurations?**
Yes, the configuration offers an option to turn off VLAN
creation/updates.

**What happens if VLAN updates are enabled and a VM administrator changes the settings of a portgroup?**
Extreme Connect will update the settings using the local
configuration data. It will not delete and recreate the portgroup, but
simply update the existing configuration.

**What happens if an end-system group is deleted and the portgroup deletion option is enabled?**
Extreme Connect will move all VMs attached to that portgroup/network
to the “VM Disconnected Systems” group and then delete the original
portgroup/network.

**If a portgroup has been deleted by Extreme Connect, can another portgroup with the same name be created manually within vSphere afterwards?**
Using its local data store, Extreme Connect will put the name of the
end-system group onto a special “deletion” stack. During each run cycle,
every module will check the stack and remove all portgroups that use the
same name until the deletion interval timer runs out. This value is set
to 2 minutes per default. After those 2 minutes have passed, a VM
administrator can safely create a portgroup of the same name without
risking it being deleted.

**Although portgroup deletion is enabled, groups are not getting deleted by Extreme Connect. What is the reason for that?**
Extreme Connect will delete all groups as long as the group is on
the deletion stack and the entry has not timed out. If too much time is
required for each run through, try increasing the deletion interval
timer so that the module has a better chance of performing the
operation.

****************
Citrix XenServer
****************

**Do I have to create a dedicated user for Extreme Connect to access the XEN Server webservice?**
No, you can use the root account on the XEN Server.

**What are the least permission requirements for the webservice user?**
The account should have at least all necessary permissions to:
| - write data to VM description fields
| - read data from VM configurations (MAC, Network)

**Although Extreme Connect seems to be running fine, I only see “n/a” in the annotation fields and no records via the Extreme Connect plugin. Why is that?**
Most likely, none of the MAC addresses of the VM is listed in the
end-system table of the NAC Manager. Make sure that authentication (at
least MAC Auth) is set up properly on the physical switch and that the
VM is actually sending some traffic.

**How often will Extreme Connect update the information within XenCenter (descriptions, networks…etc.)?**
Extreme Connect will check if the current remote data differs from
its local. If so, it will update all data that is different on the
remote service. This is especially true for the description field and it
is generally recommended not to use variables like LastSeenTime in the
annotation text, which will change very frequently and have a lot of
updates as a result.

**How does Extreme Connect determine the name of the end-system group that a VM MAC address should be added to?**
Extreme Connect creates XEN networks with the exact same name as the
corresponding Extreme Management Center end-system group. Extreme
Connect then checks all XEN networks it manages and the VMs which are
assigned to them. The MAC’s of these VMs will then be added to the
corresponding end-system group in Extreme Management Center.

**Is it possible to let Extreme Connect create networks automatically, but to let the VM administrator handle VLAN configurations?**
No, this feature is currently only supported for VMware, not for XEN.

**What happens if a XEN administrator changes the settings of a network (VLAN ID, NIC)?**
Extreme Connect will update (overwrite) the settings using the local
configuration data. It will not delete and recreate the network, but
simply update the existing configuration. For this to take place, all
VMs connected to the network will temporarily be disconnected from this
network. Then the network will be reconfigured and finally all VMs
priory connected to this network will be reconnected.

**What happens if an end-system group is deleted and the network deletion option is enabled?**
Extreme Connect will move all VMs attached to that network to the
“VM Disconnected Systems” network and then delete the original network.

**If a network has been deleted by Extreme Connect, can another network with the same name be created manually within XenCenter afterwards?**
Using its local data store, Extreme Connect will put the name of the
end-system group onto a special “deletion” stack. During each run cycle,
every module will check the stack and remove all networks that use the
same name until the deletion interval timer runs out. This value is set
to 2 minutes per default. After those 2 minutes have passed, a XEN
administrator can safely create a network of the same name without
risking it being deleted.

**Although network deletion is enabled, networks are not getting deleted by Extreme Connect. What is the reason for that?**
Extreme Connect will delete all networks as long as the group is on
the deletion stack and the entry has not timed out. If too much time is
required for each run through, try increasing the deletion interval
timer so that the module has a better chance of performing the
operation.

**I’ve set an end-system group’s description to “sync=true vlan=100” but in XEN only an internal network is being created – not an external one with the corresponding VLAN ID - why?**
In order for Extreme Connect to create an external network within
XEN two settings are necessary: VLAN ID and physical NIC to connect the
external network to.

**I’ve set an end-system group’s description to “sync=true nic=eth1” but in XEN only an internal network is being created – not an external one attached to nic eth1 without a VLAN ID - why?**
In order for Extreme Connect to create an external network within
XEN two settings are necessary: VLAN ID and physical NIC to connect the
external network to. It is not possible to create an external XEN
network without assigning a VLAN ID (all external XEN networks are
tagged).

************************************************
Adapters for XenDesktop, Hyper-V, SCVMM and SCCM
************************************************

**What is the adapter doing and how?**
The adapter is creating a Web Service bound to the IP and port that
configure within the configuration file. Extreme Connect is then making
web service calls to this adapter to retrieve data on managed
end-systems (VMs, Windows devices, etc.) and (depending on which
integration is used) also update data on the remote server (for example:
update description fields for VMs).

**What ports are needed to communicate between the Extreme Connect and the adapter?**
Only one port is required and this is the one configured on the
adapter side within its configuration file.

**Is the communication secure?**
All data sent and retrieved from/to the adapter is encrypted using
the pre-shared key which the admin defines when setting up the adapter
and installing Extreme Connect. The key itself is then automatically
encrypted.

**No information is synchronized – what else can I check?**
Check the adapter’s logfile. It will show you when the adapter has
been “called” by Extreme Connect, what powershell commands it tries to
execute and what the return values of these commands were. You need to
set the log level to “DEBUG” and restart the adapter in order for this
to print detailed logging information.

**How can I check whether the adapter’s web service is working and reachable?**
Depending on whether your Extreme Management Center server is installed
on a Windows server or on a Linux-based appliance you can use a standard
browser or a Linux tool like wget to request one of the following web
URLs (depending on the integration (adapter) you are trying to
troubleshoot):

-  XenDesktop:
   `http://<IPofAdpater>:<PortOfAdapter>/DCM_XENDESKTOP_ADAPTER <>`__

-  Hyper-V:
   `http://<IPofAdpater>:<PortOfAdapter>/DCM_HYPERV_ADAPTER <>`__

-  SCVMM: `http://<IPofAdpater>:<PortOfAdapter>/DCM_SCVMM_ADAPTER <>`__

-  SCCM:

If you get a browser error that it cannot connect or the page is not
existing you either have an issue with a firewall along the
communication path or the adapter’s web service did not start properly
on the configured IP and port. Also make sure that the configured port
for the adapter is not yet used by another service on your Microsoft
server.

*****************
Citrix XenDesktop
*****************

**Why do the usernames within Extreme Management Center NAC Manager appear as “Kerberos” usernames?**
The XenDesktop adapter uses the same webservice call as the Kerberos
snooping process. For the system’s functionality this makes no
difference: you can create user groups, rules and profiles based on
these usernames.

**After some time the usernames are deleted or disappear in NAC Manager - why?**
This can be caused by the following scenarios: First, the
corresponding XenDesktop session has ended. In this case, the adapter
resets the username on the corresponding end-system VM which will also
trigger any existing rule / NAC profile changes. Second, the Kerberos
aging timer was triggered. Within NAC Manager you can configure a period
after which the Kerberos usernames will automatically age out. If you
don’t want this timer to interfere with the XenDesktop adapter
functionality make sure to set a very high value or disable this
feature.

**Although some users have disconnected from their XenDesktop session the usernames are still active within NAC Manager - why?**
XenDesktop distinguishes between a closed/non-existing session and a
disconnected one. A session is first active, then disconnected and then
deleted. As long as the session is in the disconnected state, the
adapter still doesn’t reset the username within Extreme Management
Center. In case the user re-activates his/her session, there is no need
for the adapter to set the username and the corresponding user-profile
is already active within NAC.

*********************************************
Microsoft Hyper-V and Virtual Machine Manager
*********************************************

**How often will Extreme Connect update the information within the notes field?**
Extreme Connect will check if the current remote data differs from
its local. If so, it will update all data that is different on the
remote service. This is especially true for the notes field and it is
generally recommended not to use variables like LastSeenTime in the
notes text, which will change very frequently and have a lot of updates
as a result.

**How does Extreme Connect determine the name of the end-system group that a VM MAC address should be added to?**
Extreme Connect reads the virtual networks (virtual switches) each
VM belongs to and puts its MAC address into the corresponding end-system
group in Extreme Management Center. For this feature to work, end-system
groups with the exact same name as the virtual networks from Hyper-V
must exist within Extreme Management Center and the description field
must contain “sync=true”.
