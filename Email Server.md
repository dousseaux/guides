# Email Server Administration

Pedro Dousseau
July 12th, 2017

Based on IredMail documentation and experience from setups that I perfomed for Sigma Sensors.


#### Setup Email Server using IredMail

Enter command `hostname -f ` to view the current hostname:
```
$ hostname -f
mx.example.com
```

On Debian/Ubuntu Linux, hostname is set in two files: /etc/hostname and /etc/hosts.
/etc/hostname: short hostname, not FQDN.
```
mx
```
/etc/hosts: static table lookup for hostnames. Warning: Please list the FQDN hostname as first item.
```
# Part of file: /etc/hosts
127.0.0.1   mx.example.com mx localhost localhost.localdomain
```
Verify the FQDN hostname. If it wasn't changed after updating above two files, please reboot server to make it work.

```
$ hostname -f
mx.example.com
```

Download IredMail.:
```
$ wget www.file-address.com`
```

Install bzip2 package:

```
$ sudo apt-get install bzip2
```

Run iRedMail script:

```
$ sudo bash iRedMail.sh
```

Go through the install process **carefully**.

#### DNS Setup 

Suppose that the server mail domain name is mx.example.com and its ip address is 64.135.80.52.

The DNS setup for a new email domain registered at the email server mx.example.com would be:

|  Name 			| Type | Value                                      |
|-------------------------|-------|---------------------------------------|
|                               | MX   | 0 mx.example.com                |
|                               | TXT  | v=spf1 ip4:64.135.80.52 -all |
| dkim._domainkey |  TXT | v=DKIM1; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDPopPTChU8feoP3QDrzc506D3aD7a2Ddo/s1HpoUpei27C4fvMDhe5BhbUgFtPjf4egmx4yQpgt056qcT5dFBeOMgzXuIVmbw4QZjssrjiVUvZQ1LmmSfVsI1q/GcYd5GnIPiRJJ2xrmZ/FNAZ8pYI+mhuWewqjM8BkxYMCV3V4QIDAQAB |

You can get the DKIM value by running `$ sudo amavisd-new showkeys`




#### Using same DKIM key for all accounts

For compatibility with dkim_milter the signing domain can include a '*' as a wildcard - this is not recommended as this way amavisd could produce signatures which have no corresponding public key published in DNS. The proper way is to have one dkim_key entry for each mail domain.

If you still want to try this, please follow below steps:

Find below setting in Amavisd config file amavisd.conf:
```
dkim_key('mydomain.com', "dkim", "/var/lib/dkim/mydomain.com.pem");
```
Replace it by below line:

```
dkim_key('*', "dkim", "/var/lib/dkim/mydomain.com.pem");
```

Restart Amavisd serivce. `$ sudo amavisd-new restart`



#### Disable greylisting

Greylisting denies connections from server that never connected before.

To disable greylisting completely, please remove plugin name greylisting in iRedAPD config file /opt/iredapd/settings.py, parameter plugins =:

```
plugins = [..., 'greylisting', ...]
```

Restarting iRedAPD service is required.



#### Disable required encryption to optional

__Edit `/etc/postfix/main.cf`__
Make sure the following options are as following
```
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
# smtpd_tls_auth_only=yes
```

__Edit `/etc/postfix/master.cf`__
Find `smtpd_tls_security_level` and change its value to `may` as shown below.

```
submission inet n       -       n       -       -       smtpd
  -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=may
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_client_restrictions=permit_sasl_authenticated,reject
  -o content_filter=smtp-amavis:[127.0.0.1]:10026
```

Reload postfix. `$ sudo postifx reload`

#### Check and Fix Fail2ban

fail2ban uses iptables to block traffic. If you would want to see the IP addresses that are 
currently blocked, run:

```
$ iptables -L -n 
```
Look for the various chains named fail2ban-something, where something points 
to the fail2ban jail (for instance, Chain f2b-sshd refers to the jail sshd). If you only want to 
remove the block for a single IP address <IP> for a given jail <JAIL>, fail2ban offers its own 
client:

```
fail2ban-client set <JAIL> unbanip <IP>
```

Alternatively you can use line numbers. First, list the iptables rules with line numbers:

```
sudo iptables -L -n --line-numbers
```

Next you can use

```
sudo iptables -D f2b-somejail <linenumber> 
# Example:
sudo iptables -D f2b-postfix 1
```

