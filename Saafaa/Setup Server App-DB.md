**Network**
	nano etc/network/interfaces
	iface ens.X inet static
	allow-hotplug ens32
	address
	netmask 255.255.255.0
	gateway 


```
service networking  restart
```

**DNS**
	nano /etc/resolv.conf
	nameserver X.X.X.X
	nameserver X.X.X.X


**SSH setup**
	etc/ssh/sshd_config
	uncomment this: PremitRootLogin yes

**Access Port**
	5432 db 
	22 ssh 