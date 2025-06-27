# how to manually configure dns on a vps host running ubuntu OS using command-line

To manually configure DNS on an Ubuntu VPS using the command line,you'll primarily interact with the `/etc/resolv.conf` file and potentially the `netplan` configuration for more persistent changes.
First, you'll need to identify the DNS servers you want to use, often provided by your hosting provider or public DNS servers like Google's (8.8.8.8, 8.8.4.4). 

*Steps for modifying `/etc/resolv.conf` (temporary changes):*

Open the file: Use a text editor like nano to open the `resolv.conf` file with root privileges: 

```code
sudo nano /etc/resolv.conf
```
*Add DNS servers:* Add lines specifying the DNS servers, one per `nameserver` entry: 
```code
nameserver 8.8.8.8
nameserver 8.8.4.4
```
*Save and close the file:* In `nano`, press `Ctrl+X`, then `Y`, then `Enter`.

*Test the changes:* Use the `resolvectl` command to see the current DNS configuration and verify the changes.
You can also try pinging a domain name to see if resolution is working. 
`*Note:* Changes to /etc/resolv.conf are often overwritten on reboot or by network manager, so this method is usually temporary.`

## Steps for configuring DNS using `netplan` (more persistent changes):

1. Identify your network interface:
Use `ip addr` or `nmcli device status` to find your network interface name (e.g., `eth0`, `ens3`).

2. Locate the netplan configuration file:
This is usually found in `/etc/netplan/`. The file name might be something like `01-network-manager-all.yaml` or `50-cloud-init.yaml`.

3. Edit the netplan configuration:
Open the file with root privileges using `sudo nano /etc/netplan/your_config_file.yaml`.

4. Modify the `nameservers` section:
Find the `ethernets` section for your interface and add or modify the `nameservers` section. Here's an example: 
```code
   network:
     version: 2
     renderer: networkd
     ethernets:
       your_interface_name:  # Replace with your interface name
         dhcp4: true
         nameservers:
           addresses: [8.8.8.8, 8.8.4.4]
           search: [your_domain, other_domain] # Add your domain(s) here
```
5. Apply the changes:
Run `sudo netplan apply`.

6. Verify:
Use `resolvectl` and ping tests as described above to confirm the changes. 

## Important Considerations:

- Security:
If you're setting up a DNS server, you'll need to install and configure BIND or another DNS server software and configure it appropriately, including setting up zones and access controls. 

- IPv6:
Ensure your configuration also handles IPv6 DNS if needed.

- Firewall:
Configure your firewall (e.g., ufw) to allow DNS traffic (ports 53 UDP and TCP) if you are running a local DNS server. 

*This video demonstrates how to set up a DNS server on Ubuntu:*
[video]('https://www.youtube.com/watch?v=RMCw7NguaNw&t=1298')
