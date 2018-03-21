Contract
========

In order for different EPGs to be able to communicate, they must have a contract.
Server provides the contract and Client consumes a contract.

.. image:: contract.png
   :width: 500px
   :alt: Contract

Contracts exist in VRF. To know the VRF ID, you can run the following command:

.. code-block:: console

	leaf103# show system internal epm vrf all


	+--------------------------------+--------+----------+----------+------+--------
	               VRF                  Type    VRF vnid  Context ID Status Endpoint
	                                                                          Count 
	+--------------------------------+--------+----------+----------+------+--------
	 black-hole                       Tenant   16777200   3          Up     0       
	 tshoot:tshoot-vrf                Tenant   2949120    6          Up     1       
	 overlay-1                        Infra    16777199   4          Up     2       


	leaf103# show zoning-rule scope 2949120
	Rule ID         SrcEPG          DstEPG          FilterID        operSt          Scope           Action                              Priority       
	=======         ======          ======          ========        ======          =====           ======                              ========       
	4102            0               49154           implicit        enabled         2949120         permit                              any_dest_any(15)
	4103            0               0               implicit        enabled         2949120         deny,log                            any_any_any(20)
	4104            0               0               implarp         enabled         2949120         permit                              any_any_filter(16)
	4105            0               15              implicit        enabled         2949120         deny,log                            any_vrf_any_deny(21)

Reference
----------
#. Verify Contracts and Rules in the ACI Fabric https://www.cisco.com/c/en/us/support/docs/cloud-systems-management/application-policy-infrastructure-controller-apic/119023-technote-apic-00.pdf