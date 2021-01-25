# VPN Server

Author: mqus

Configuring a VPN server firewall can be a bit more complicated.
This will only cover nftables, not your VPN server itself. 

We will also assume that you already selected a subnetwork adress for your VPN
(eg. `10.8.0.0/24`) and will refer to it as `$vpn_subnet$` in the config 
(you'll have to replace that part with your subnet) and you know the network interface name 
`$vpn_if$` of that vpn connection (eg. `tun0` or more broadly: `tun*`). 

You also should be aware of the network interface 
where your connections should be forwarded to, eg. `eth0`. 
We will refer to that interface as `$outside_if$`.

We will basically add two things to our configuration.
1. A rule that allows new incoming vpn connections (you need to know the udp/tcp port of your server for this (the placeholder being `$vpn_port$`))
   - An example rule for OpenVPN is included below (for udp connections)
2. A rule that allows forwarding of packets recieved via a vpn connection to the network.
   We will achieve this by adding rules to the forward chain.
3. A rule that enables NAT so that packages can be routed back to the VPN painlessly.
   We will do this by creating a new table with a postrouting chain containing a rule 
   which will masquerade the original adress/port pair.

Configuration file (e.g. `/etc/nftables.conf`):
```
table inet filter{


	chain input {
		[...]
		
		# allow generic VPN connections to the Server
		$tcp/udp$ dport $vpn_port$ accept
		
		# allow OpenVPN
		# udp dport 1194 accept
		
	}
	
	chain forward {
		#Drop forwarded packets if they are not matched
		type filter hook forward priority 0; policy drop;

		# allow existing connections 
		ct state related,established accept
		
		# allow packats from vpn interface
		iifname $vpn_if$ oifname $outside_if$ accept

	}
	
	chain output {
		[...]
	}

}

# create a ipv4 table only for NAT entries (you need both chains even if they're empty)
table ip nat {
	chain postrouting {
		type nat hook postrouting priority 100;
	
		# enable NAT for VPN
		iifname $vpn_if$ oifname $outside_if$ ip saddr $vpn_subnet$ masquerade
	
	}
	
	chain prerouting {
		type nat hook prerouting priority 0;
	
	}

}




```
`nft`-commands that will do the same:

```
# allow incoming vpn connections
nft add rule filter input $tcp/udp$ dport $vpn_port$ accept

# example Rule for OpenVPN
# nft add rule filter input udp dport 1194 accept

# add packet forwarding
nft add chain inet filter forward { type filter hook forward priority 0 \; policy drop}
nft add rule filter forward ct state related,established accept
nft add rule filter forward iifname $vpn_if$ oifname $outside_if$ accept

# add NAT for all outgoing IPv4 connections (setup NAT table and chains, add masquerading)
nft add table ip nat
nft add chain ip nat prerouting { type nat hook prerouting priority 0 \; }
nft add chain ip nat postrouting { type nat hook postrouting priority 100 \; }

nft add rule nat postrouting iifname $vpn_if$ oifname $outside_if$ ip saddr $vpn_subnet$ masquerade
```
