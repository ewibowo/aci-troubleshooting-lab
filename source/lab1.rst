.. sectnum::

Fabric Discovery
===================

Physical Topology
--------------------
.. image:: physical-topo.png
   :width: 600px
   :alt: Physical 

APIC Cluster Connectivity
----------------------------

.. image:: apic-cluster.png
   :width: 600px
   :alt: APIC Cluster connectivity

Erase Configuration
----------------------

To erase configuration of APIC so that we can re-setup APIC:

Sometimes KVM cannot launch because of Java issues. 
If you encounter such a proble, you can use Serial Over LAN as follows.
SSH to CIMC of the APIC::

   ssh admin@10.66.88.178 

Enable the Serial Over LAN (SoL)::

   cimc# 
   cimc# scope sol
   cimc /sol # set enabled yes
   cimc /sol *# set baud-rate 115200
   cimc /sol *# commit 
   cimc /sol # connect host
   CISCO Serial Over LAN:
   Press Ctrl+x to Exit the session

   Application Policy Infrastructure Controller
   apic1 login: admin
   Password:
   Last login: Thu Mar 15 00:31:36 on tty1
   apic# acidiag touch setup
   apic# acidiag reboot

To erase configuration of leaf/spine switch so that they can automatically retrieve configuration from APIC::

  switch# acidiag touch clean
  switch# reload


Fabric Initial Setup
--------------------

Once the APIC is rebooted, it will start in the initial config wizard::

  Starting Setup Utility                                                          
                                                                                  
                                                                                  
  This setup utility will guide you through the basic configuration of            
  the system. Setup configures only enough connectivity for management            
  of the system.                                                                  
                                                                                  
  *Note: setup is mainly used for configuring the system initially,               
  when no configuration is present. So setup always assumes system                
  defaults and not the current system configuration values.                       
                                                                                  
  Press Enter at anytime to assume the default values. Use ctrl-c                
  at anytime to restart from the beginning.


  Cluster configuration ...
    Enter the fabric name [ACI Fabric1]: ACI Training
    Enter the fabric ID (1-128) [1]: 
    Enter the number of controllers in the fabric (1-9) [3]: 
    Enter the POD ID (1-9) [1]: 
    Enter the controller ID (1-3) [1]: 
    Enter the controller name [apic1]: 
    Enter address pool for TEP addresses [10.0.0.0/16]: 
    Note: The infra VLAN ID should not be used elsewhere in your environment 
          and should not overlap with any other reserved VLANs on other platforms.
    Enter the VLAN ID for infra network (2-4094): 4094
    Enter address pool for BD multicast addresses (GIPO) [225.0.0.0/15]: 

  Out-of-band management configuration ...
    Enable IPv6 for Out of Band Mgmt Interface? [N]: 
    Enter the IPv4 address [192.168.10.1/24]: 10.66.88.181/27
    Enter the IPv4 address of the default gateway [None]: 10.66.88.161
    Enter the interface speed/duplex mode [auto]: 

  admin user configuration ...
    Enable strong passwords? [Y]: N
    Enter the password for admin: 

    Reenter the password for admin: 

  Cluster configuration ...
    Fabric name: ACI Fabric1
    Fabric ID: 1
    Number of controllers: 3
    Controller name: apic1
    POD ID: 1
    Controller ID: 1
    TEP address pool: 10.0.0.0/16
    Infra VLAN ID: 4094
    Multicast address pool: 225.0.0.0/15

  Out-of-band management configuration ...
    Management IP address: 10.66.88.181/27
    Default gateway: 10.66.88.161
    Interface speed/duplex mode: auto

  admin user configuration ...
    Strong Passwords: N
    User name: admin
    Password: ********

  The above configuration will be applied ...

  Warning: TEP address pool, Infra VLAN ID and Multicast address pool
           cannot be changed later, these are permanent until the
           fabric is wiped.

  Would you like to edit the configuration? (y/n) [n]:n


Configuration Verification
-----------------------------

Ensure the bond interace is up
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Check which active interface is connected to the leaf::

  apic# cat /proc/net/bonding/bond0
  Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

  Bonding Mode: fault-tolerance (active-backup)
  Primary Slave: None
  Currently Active Slave: eth2-1 <<< active interface 
  MII Status: up
  MII Polling Interval (ms): 60
  Up Delay (ms): 0
  Down Delay (ms): 0

#. Ensure the lldp information is correct (infra vlan)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Check the lldp neighbours on APIC1::

  apic# acidiag run lldptool in eth2-1
  Port Description TLV
          topology/pod-1/paths-101/pathep-[eth1/45]
  Cisco Infra VLAN TLV
          4094

Check the lldp neighbours on connected Leaf::
  switch# show lldp neighbor
 
If the lldp neigbor empty or showing mac address, that means the LLDP is enabled on the VIC card of APIC. As a result, the VIC consumes the LLDP and the APIC cannot respond. To disable LLDP on VIC:

SSH as user admin to CIMC of the APIC ::

  CIMC# scope chassis
  CIMC /chassis # show adapter
  PCI Slot Product Name Serial Number Product ID Vendor
  -------- -------------- -------------- -------------- --------------------
  1 UCS VIC 1225 FCHxxxxxxxx UCSC-PCIE-C... Cisco Systems Inc
  CIMC /chassis # scope adapter 1
  CIMC /chassis/adapter # show detail | grep LLDP
  LLDP: Enabled
  CIMC /chassis/adapter # set lldp disabled
  CIMC /chassis/adapter *# commit
  New VNIC adapter settings will take effect upon the next server reset
  CIMC /chassis/adapter # exit
  CIMC /chassis # power cycle

Source: https://supportforums.cisco.com/legacyfs/online/attachments/document/files/apic-vic-lldp-fn.pdf
