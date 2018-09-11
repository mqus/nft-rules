# SSDP Client Configuration

Author: mqus

SSDP is a multicast protocol for discovering devices in your local network.
This snippet focuses on devices which want to discover some other devices.
As the protocol is a bit unusual, it needs some special rules to open the firewall a bit if needed.

This configuration will detect if a ssdp packet requesting discovery is sent 
(usually to the broadcast adress of 239.255.255.250 and the udp port 1900) 
And will allow any packets sent to the local udp port (from which the request was sent) for 5 seconds.
These 5 seconds are chosen rather arbitrarily and have to be long enough so all responses can be received but also short 
enough so the ports are secured from outside entry.

Its assumed that a rule accepting packages with the right conntrack is already established 
(as written in the config file below)

The following config creates a named set containing the udp port from which a ssdp discovery 
message was sent, named `ssdp_out` and adds the port of the message when discovered (in the `output` chain)

Configuration file (e.g. `/etc/nftables.conf`):
```
table inet filter{

	# A set containing the udp port(s) to which ssdp replies are allowed.
	set ssdp_out {
		type inet_service
		timeout 5s
	}

	chain input {
		[...]
		# allow established/related connections
		ct state {established, related} accept

		
		# allow incoming ssdp replies
		udp dport @ssdp_out accept
		

	}

	chain output {
		[...]
		# Remember port of outgoing SSDP message
		ip daddr 239.255.255.250 udp dport 1900 set add udp sport @ssdp_out
		# IPv6 (link-local/site-local/organization-local/global)
		ip6 daddr {FF02::C, FF05::C, FF08::C, FF0E::C} udp dport 1900 set add udp sport @ssdp_out
	}

}
```
`nft`-commands that will do the same:

```
# Create a set for remembering the port on which ssdp replies will be received
nft add set filter ssdp_out {type inet_service \; timeout 5s \;}

# Create a rule for adding the ports to the set
nft add rule filter output ip daddr 239.255.255.250 udp dport 1900 set add udp sport @ssdp_out

# The same for ipv6
nft add rule filter output ip6 daddr {FF02::C, FF05::C, FF08::C, FF0E::C} udp dport 1900 set add udp sport @ssdp_out

# Create a rule for accepting any ssdp packets going to a remembered port.
nft add rule filter input udp dport @ssdp_out accept
```
