# arpflooder

Simple python script to send **floody arp** request packet to maintain forwarding tables of the ethernet switches. Intended to be used as nagios service check script.

## How it works

TBD

## Prerequisites

- Linux / iproute
    - depends on `ip neigh` command
- Python
- Scapy
- netaddr (python package)
- netifaces (python package)

Tested with following environments:

- CentOS 6.4
    - kernel-2.6.32-358.123.2.openstack.el6.x86_64
    - iproute-2.6.32-130.el6ost.netns.2.x86_64
    - Python 2.6.6 (base)
    - Scapy 2.0.0 (epel)
    - python-netaddr-0.7.5-4.el6.noarch (base)
    - python-netifaces-0.5-1.el6.x86_64 (epel)
    - Nagios 3.5.1 (epel)

## Usage

You need root privilege.

```bash
sudo /path/to/arpflooder/flooder.py -i eth0 -d 192.0.2.1
```

### Options

- -i/--interface *dev*  
**(Required)** Interface from which all packets are sent out and received. This interface must have one IP address assigned and be set promiscuous mode on. If the specified interface is virtual device e.g. VLAN sub interface, then parent device must also be set promiscuous mode on.

- -d/--pdst *target_ip*  
**(Required)** Target IP address. Floody ARP requests are sent to this host.

- -S/--hwsrc *mac*  
MAC address set to *Source Hardware Address* field of floody ARP request packet. The target will send ARP reply to this MAC. **Specify any address which is not actually used in the network** to make the reply floods among all switches.  
Default: 00:50:56:be:ee:ef

- -t/--timeout *timeout*  
Timeout in seconds waiting arp replies after every request made.  
Default: 3 (seconds)

### Use with nagios

If you are running nagios as non-pirivileged user e.g. nagios or nobody, you need to add sudo entry first so that the user can invoke it without any password.

```
nagios ALL=(ALL) NOPASSWD: /path/to/arpflooder/flooder.py
```

Next define a nagios command,

```
define command {
  command_name    flooder
  command_line    /usr/bin/sudo /path/to/arpflooder/flooder.py -i eth0 -d $HOSTADDRESS$ -S 00:50:56:be:ee:ef
}
```

Then use it:

```
define service {
 use                     generic-service
 host_name               TARGET_HOST
 service_description     FLOODER
 check_command           flooder
}
```

### logging

TBD