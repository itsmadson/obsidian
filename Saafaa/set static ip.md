in network INTERFACE.D
etc/network/interfaces
iface ens.X iner static
allow-hotplug ens32
address
netmask
gateway 


service networking  restart

nano /etc/resolv.conf
nameserver X.X.X.X


ssh setup 
etc/ssh/sshd_config
uncommin PremitRootLogin yes

access ports:
5432 db 
22 ssh 