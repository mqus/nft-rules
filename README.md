# nft-rules
A Collection of useful nftables rules for all applications (From simple 'open port x' over vpn with NAT to ssdp/upnp)

**WARNING: The authors are not responsible for the security of your network. 
Please do use the examples provided here with care and try to understand the examples before copying it into your configuration.**

All examples and documentation shown here are provided under the Creative Commons Attribution 4.0 International License (CC-BY-4.0).

All Snippets assume a simple stateful firewall including a filter table, an input and output chain. Some help 
for achieving this can be found on the [Archlinux Wiki](https://wiki.archlinux.org/index.php/Nftables#Simple_IPv4.2FIPv6_firewall).
If a snippet doesn't work for you, file an issue and we'll try to help you and maybe improve the snippet in the process.

## Contribute
If you want to contribute, please read [this](CONTRIBUTING.md)


## Index

- [Port Knocking](https://wiki.nftables.org/wiki-nftables/index.php/Port_knocking_example)
- [SSH](files/SSH.md) (Please Contribute!)
- [Web Server (HTTP+HTTPs)](files/HTTP.md) (Please Contribute!)
- [VPN with ipv4-NAT](files/VPN.md)
- [SSDP Client](files/SSDP_client.md)


## Links

[The official nftables Wiki](https://wiki.nftables.org)