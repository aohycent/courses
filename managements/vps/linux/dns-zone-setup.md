# Setting up a DNS zone with AlmaLinux

1. First of all, you need to connect to your server via SSH.

On Linux and MacOS, you can use the following command:

ssh root@`IP` -p`PORT`
`IP`: is the IP of the server you are connecting to
`PORT`: connection port - 22 for a VPS/Dedicated server by default

In our example:
```bash
ssh root@1.2.3.4 -p22
```

For Windows, you can use PuTTY software.

2. Once you are logged in, make sure your server is fully updated. To do this, run the following command:
```bash
dnf -y update
```

3. Once you have done this, install BIND using the following command:
```bash
dnf -y install bind bind-utils
```

Once the command is executed, the BIND9 DNS server is now installed on your system and you can start configuring it.

4. By default, only localhost is allowed. However, we need our nameservers to reply to queries from all over the world, so it is required to change this configuration.

Open the main configuration file using the command:
```bash
nano /etc/named.conf
```

In the file, within the `options{..}` include or change the following parameters to `{any;}`:
```conf
options{
        listen-on port 53 {any;}
        allow-query  {any;}
}
```
[! example](https://namecheap.simplekb.com/SiteContents/2-7C22D5236A4543EB827F3BD8936E153E/media/alma1.1.png)

5. Then, we need to include named.conf.local to the application configuration which we will use to define our DNS zones.

Add the following line to the end of the file:
```conf
include "/etc/named/named.conf.local";
```
[! example](https://namecheap.simplekb.com/SiteContents/2-7C22D5236A4543EB827F3BD8936E153E/media/alma1.2.png)


Press `Control+X`, type `Y` and press `Enter/return`. It will save changes and close the file.

6. In order to tell BIND9 to look for the file /etc/named/nctest.info to find the DNS zone for nctest.info, open this file:
```bash
nano /etc/named/named.conf.local
``

Here, it should be empty. Now insert the following to the file:
```bash
zone "nctest.info" {
        type master;
        file "/etc/named/nctest.info";
 };
```
Keep in mind that nctest.info should be replaced with your own domain name.

Press `Control+X`, type `Y` and press `Enter/return`.

7. Now, open the zone file and add the necessary DNS records there:
```bash
nano /etc/named/nctest.info
```
The following text also needs to be added to the file:
```bash
$TTL 86400
@   IN  SOA     ns1.nctest.info. root.nctest.info. (
        2019021501  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
@       IN  NS          ns1.nctest.info.
@       IN  NS          ns2.nctest.info.
ns1     IN  A           1.2.3.4
ns2     IN  A           1.2.3.4
@       IN  A           1.2.3.4
```

Do not forget to change the domain nctest.info and the IP address 1.2.3.4 to the required values.

The Serial number should be changed according to the current date in a format `YYYYMMDDXX`, 
where `XX` represents the number of the zone changes on a specific day.
If you only create the zone, put `01` there.
If it’s the third time you modify the DNS zone a day, put `04` instead of XX.

You can also add any needed DNS records in this file. It will store the zone of your domain name, 
and to make any changes to its zone, you will need to open this file and edit it. 
When you perform any changes, do not forget to increase the Serial number (according to the current date).

Here is an example of a DNS zone: [! example](https://namecheap.simplekb.com/SiteContents/2-7C22D5236A4543EB827F3BD8936E153E/media/alma1.4.png)

Once done, press `Control+X`, type `Y` and press `Enter/return`.

8. The next step would be making sure that the BIND9 directory has the correct permissions and the correct owner:
```bash
chmod -R 755 /etc/named

chown -R named:named /etc/named
```

9. Now that the initial configurations are done, let’s check to see if everything is properly configured. To do so, run the following commands:
```bash
named-checkconf /etc/named.conf

named-checkconf /etc/named/named.conf.local
```

If these commands do not return anything, it means everything is properly configured.

10. Now check the DNS zone you created:
```bash
named-checkzone nctest.info /etc/named/nctest.info
```

The output should be as follows:
```
zone nctest.info/IN: loaded serial 2019021501
OK
```
11. As the final step, restart your DNS server:
```bash
service named restart
```

12. Make sure that tcp/udp port 53 is opened in the firewall. You can open it with the following command:
```bash
firewall-cmd  --add-service=dns --zone=public  --permanent

firewall-cmd --reload
```

That’s it! You now can check to see how it works using the dig command.

## IMPORTANT: 
To ensure it’s working, your private nameservers must be registered with your respective domain registrar.

You may have noticed we only configured a forward DNS zone.
Why? Because it’s not possible to configure a reverse DNS zone and set up the PTR record for your server’s IP address in the same way.
