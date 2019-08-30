==============
VMware vSphere
==============

The Vmware vSphere integration offers provisioning of virtual machines
in the network as well as automating the creation of virtual networks
based on end-system access groups. In addition, data within Extreme
Management Center is enriched for each end-system and conversely made
available within vSphere.

.. contents:: Contents
   :depth: 3

|image1|


Module Configuration
^^^^^^^^^^^^^^^^^^^^

======================= ====================================================
Configuration Option    Description
======================= ====================================================
Username:               Username used to connect to the vSphere web service.
                       
                        Read/Write/Execute permissions required.
Password:               Password used to connect to the vSphere web service.
VMware Webservice URL : Web service URL of the VMware vSphere server.
Module enabled :        Enables and Disables Module.
======================= ====================================================

-  **Outgoing data format**: The format of the Extreme Control data
      (like last seen time, switch IP, switch port, etc.) that is
      written to the description fields of the VMs within VMware or XEN.
      You can customize the appearance and what information you want to
      include/exclude from there. Hint: For the VMware vSphere client
      the annotation field is limited in size. The default outgoing
      format is very close to the maximum string length allowed for this
      field. If you want to add additional information to this field
      consider replacing it with some of the existing default value.

-  **Format of the incoming data**: The format of the data that is
      coming from VMware or XEN and that is written to the custom field.

-  **Create Private VLAN Entries**: If set to false, the Datacenter
      manager will not automatically create any pVLAN entries on
      dvSwitches even if you configured any. This feature is disabled
      per default and needs to be enabled manually if needed.

-  **Create Portgroups from End-system Groups**: If set to true, the
      Datacenter manager will automatically create new portgroups within
      VMware based on the Extreme Control NAC end-system groups and your
      other configuration.

-  **Update Portgroup VLAN IDs**: Only useful if the setting above is
      set to true. If you change the “vlan=XXXX” value within an
      end-system group this setting will automatically also change your
      portgroup VLAN IDs accordingly.

-  **Use Global End-system Groups:** Only if this is set to true, the
      VMware module will have access to the global end-system groups
      that are provided by the Extreme Control module (NAC end-system
      groups) within the main module. This is necessary if you want to
      automatically create portgroups based on Extreme Control NAC
      end-system groups.

-  **Enable Custom Attributes**: En-/Disables the creation and updates
      of Custom Attributes for vCenter Servers.

-  **Custom Attributes Data Format**: This text field allows the
      configuration of Custom Attributes for vCenter Servers. Extreme
      Connect will create and update these attributes for each VM and
      allow for searching and sorting for this data within vCenter. Each
      attribute has to be configured on a single line and follow the
      format: NAME=VALUE where NAME is the name of the Custom Attribute
      and VALUE is a free text that may utilize all variables that are
      available in the “Outgoing data format” option. If a VM should use
      more than one network interface, the data for each variable is
      presented as “NIC1DATA/NIC2DATA/…”.

-  **Deletion Group**: Name of the portgroup that a VM will be
      redirected to if it's current endsystem group is deleted.

-  | **Port Group Import**: Enables the automatic creation of
        endsystemgroups in Extreme Control based on port groups. The
        port group name will be used for the endsystem group. Be aware
        that the delimiter also applies here. In the default
        configuration, the text after the last delimiter will be
        truncated from the name.
      | i.e. **MyPortGroup_VLAN1_dvSwitch0** will be imported as
        **MyPortGroup_VLAN1** in Extreme Control. VLAN IDs will be
        updated if they change.

-  **Automatic Enforce after import**: Enables the automatic enforcement
      of all NAC appliances and the policy domain (only for extended
      import) if a portgroup was imported.

-  | **Extended PortGroup Import**: Also creates NAC Configuration and
        policy profiles during PortGroup Import. Requires the options
        for **NAC Configuration**, **Policy Domain** and **Forward as
        Tagged** also to be defined. Be aware that the truncated port
        group name will also be used as the VLAN name and must adhere to
        naming limitations.
      | In a special case, a VNI can be supplied by prefixing
        “VNI-#####-“ (with ##### being the VNI ID) to the portgroup
        name. i.e. “VNI-1234-PortGroup” will create a policy/control
        configuration with the VLAN ID set in the portgroup and the VLAN
        name VNI-1234-PortGroup. An EXOS switch can then use the
        VNI-1234 part to setup the VxLAN mapping for that VLAN.

-  **Add VNI to Policy Map** Adds the VNI ID from the Portgroup name to
      one of the custom fields in the policy mapping configuration in
      Control. This can be used to supply the VNI to a target switch
      using RADIUS to create a dynamic VxLAN configuration.

-  **Enable PortGroup Import Removal**: Delete the NAC Configuration
      and/or End-System Group if the portgroup is deleted.

-  **Hypervisor Import:** Creates a Device in XMC Network for each
      Hypervisor, using the pNIC, vNIC and (d)vSwitch portgroups to
      generate the Device Ports. LLDP data will be used if present to
      indicate neighbors on ports.

-  **EndSystem Events:** Updates the Control EndSystem Table if Radius
      or Kerberos authentication is not available. Events will use the
      Hypervisor Device as the connecting Switch instead of the physical
      LAN switch provided via Radius.

Stop then start the Extreme Management Center services (refer to Extreme
Connect Installation section for instructions).


Verification
^^^^^^^^^^^^

Within the vSphere Client, click on a virtual machine and then on the
“Summary” tab on the right side. At the bottom of this tab there should
be an annotations field that should contain the corresponding data from
Extreme Management Center (for example, information on the switch port
and switch IP to which this VM is physically connected).

|image2|


.. |image1| image:: media/image1.png
   :width: 5.77014in
   :height: 3.42083in
.. |image2| image:: media/image2.png
   :width: 3.06126in
   :height: 3.88857in
