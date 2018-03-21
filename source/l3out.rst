L3out
=====

There several points to verify during troubleshooting:

* Fabric Policy – BGP Route Reflector
* BD and L3out association
* BD subnet advertised externally
* L3out - External EPG
* Contract between Internal EPG and External EPG

To check whether BGP route reflector has been configured, we can check the BGP VPNV4 neigborship in vrf overlay-1:

.. code-block:: console

	leaf103# show bgp vpnv4 unicast summary vrf overlay-1
	BGP summary information for VRF overlay-1, address family VPNv4 Unicast
	BGP router identifier 10.0.32.92, local AS number 6500
	BGP table version is 47, VPNv4 Unicast config peers 1, capable peers 1
	6 network entries and 8 paths using 1032 bytes of memory
	BGP attribute entries [2/288], BGP AS path entries [0/0]
	BGP community entries [0/0], BGP clusterlist entries [1/4]

	Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
	10.0.32.90      4  6500     400     407       47    0    0 06:22:35 2         

To check whether the prefix has been learnt in BGP VPNV4:

.. code-block:: console

	leaf103# show bgp vpnv4 unicast vrf overlay-1
	BGP routing table information for VRF overlay-1, address family VPNv4 Unicast
	BGP table version is 47, local router ID is 10.0.32.92
	Status: s-suppressed, x-deleted, S-stale, d-dampened, h-history, *-valid, >-best
	Path type: i-internal, e-external, c-confed, l-local, a-aggregate, r-redist
	Origin codes: i - IGP, e - EGP, ? - incomplete, | - multipath

	   Network            Next Hop            Metric     LocPrf     Weight Path
	Route Distinguisher: 10.0.32.91:2
	*>i192.168.198.0/24   10.0.32.91               0        100          0  ?
	*>i9.9.9.9/32         10.0.32.91               5        100          0  ?

	Route Distinguisher: 10.0.32.92:2     (VRF tshoot:tshoot-vrf)
	*>r192.168.197.0/24   0.0.0.0                  0        100      32768  ?
	*>r192.168.198.0/24   0.0.0.0                  0        100      32768  ?
	* i                   10.0.32.91               0        100          0  ?
	*>r192.168.200.0/24   0.0.0.0                  0        100      32768  ?
	*>r9.9.9.9/32         0.0.0.0                  5        100      32768  ?
	* i                   10.0.32.91               5        100          0  ?

To check the EPG class ID:

.. code-block:: console

	leaf103# 
	module-1# show system internal aclqos prefix

	Vrf Vni Addr           Mask     Scope Class  Shared Remote

	======= ============== ======== ===== ====== ====== ======
	2719745 0::/0 0::/0 3     15     FALSE FALSE
	2719745 0.0.0.0        ffffffff 3     15     FALSE FALSE
	2949120 0::/0 0::/0 4     15     FALSE FALSE
	2949120 0.0.0.0        ffffffff 4     15     FALSE FALSE
	2949120 9.9.9.9        0        4     16388  FALSE FALSE
	2916352 0::/0 0::/0 5     15     FALSE FALSE
	2916352 0.0.0.0        ffffffff 5     15     FALSE FALSE
	2293760 0::/0 0::/0 6     15     FALSE FALSE
	2818048 0::/0 0::/0 6     15     FALSE FALSE
	2293760 0.0.0.0        ffffffff 6     15     FALSE FALSE
	2818048 0.0.0.0        ffffffff 6     15     FALSE FALSE
	2129920 0::/0 0::/0 7     15     FALSE FALSE
	2129920 0.0.0.0        ffffffff 7     15     FALSE FALSE
	2392064 0::/0 0::/0 8     15     FALSE FALSE
	2392064 0.0.0.0        ffffffff 8     15     FALSE FALSE
	3112960 0::/0 0::/0 9     15     FALSE FALSE
	3112960 0.0.0.0        ffffffff 9     15     FALSE FALSE
	3080192 0::/0 0::/0 10    15     FALSE FALSE
	3080192 0.0.0.0        ffffffff 10    15     FALSE FALSE
	2588672 0::/0 0::/0 11    15     FALSE FALSE
	2916353 0::/0 0::/0 11    15     FALSE FALSE
	2588672 0.0.0.0        ffffffff 11    15     FALSE FALSE
	2916353 0.0.0.0        ffffffff 11    15     FALSE FALSE
	2392065 0::/0 0::/0 12    15     FALSE FALSE
	2392065 0.0.0.0        ffffffff 12    15     FALSE FALSE
	2097152 0::/0 0::/0 14    15     FALSE FALSE
	2097152 0.0.0.0        ffffffff 14    15     FALSE FALSE
	2621442 0::/0 0::/0 15    15     FALSE FALSE
	2621442 0.0.0.0        ffffffff 15    15     FALSE FALSE
	2555904 0::/0 0::/0 16    15     FALSE FALSE
	2555904 0.0.0.0        ffffffff 16    15     FALSE FALSE
	3080193 0::/0 0::/0 18    15     FALSE FALSE
	3080193 0.0.0.0        ffffffff 18    15     FALSE FALSE
	2785280 0::/0 0::/0 21    15     FALSE FALSE
	2785280 0.0.0.0        ffffffff 21    15     FALSE FALSE

	Shared Addr    Mask     Scope Class  RefCnt

	============== ======== ===== ====== ======
	module-1# 

To check existing BD subnets (pervasive gateways):

.. code-block:: console

   apic# moquery -c fvSubnet

   