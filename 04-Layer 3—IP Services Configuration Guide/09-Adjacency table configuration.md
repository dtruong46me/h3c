
# Displaying the adjacency table

## About the adjacency table

The adjacency table stores information
about directly connected neighbors for IP forwarding. The neighbor information
in this chapter refers to non-Ethernet neighbor information.

This table is not user configurable. The neighbor
information is generated, updated, and deleted by link layer protocols through
negotiation or through manual configuration. An adjacency entry includes the
following information:

·     Neighbor network layer address (next hop).

·     Output interface.

·     Link layer protocol type.

·     Link layer address.

When forwarding an IP packet, the device
performs the following tasks:

·     Searches the FIB to find the output interface
and next hop.

·     Uses the output interface and next hop address
to search the adjacency table for link layer forwarding information. 

 

|  | NOTE:  Ethernet and non-Ethernet neighbor information is stored and managed together. |
| --- | --- |

‌

## Display and maintenance commands for adjacency table

|  | NOTE:  The device only supports displaying adjacency entries about tunnel interfaces whose link layer protocol is P2P. |
| --- | --- |

‌

Execute display commands in any view.

 

| Task | Command |
| --- | --- |
| Display IPv4 adjacency table information. | display adjacent-table { all \| physical-interface interface-type interface-number \| routing-interface interface-type interface-number \| slot slot-number } \[ count \| verbose ] || Display IPv6 adjacency table information. | display ipv6 adjacent-table { all \| physical-interface interface-type interface-number \| routing-interface interface-type interface-number \| slot slot-number } \[ count \| verbose ] |


‌

 

