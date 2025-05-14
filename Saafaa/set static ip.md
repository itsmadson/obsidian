in network INTERFACE.D
etc/network/interfaces
iface ens.X iner static
allow-hotplug ens32
address
netmask
gateway 
dns

service network start
set dns resolv.conf

ssh setup 
etc/ssh/sshd_config
uncommin premitroot
