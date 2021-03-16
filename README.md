# route

#firewall

iptables -A FORWARD -j DROP\
iptables -A INPUT -j DROP\
iptables -A OUTPUT -j DROP\
iptables -I INPUT 1 -i lo -j ACCEPT\
iptables -I OUTPUT 1 -o lo -j ACCEPT\
iptables -I FORWARD 1 -s 2.0.0.0/8 -j ACCEPT \
iptables -I FORWARD 2 -d 2.0.0.0/8 -j ACCEPT \	
iptables -I FORWARD 3 -d 50.50.0.0/25 -p tcp -j ACCEPT \
iptables -I FORWARD 4 -s 50.50.0.0/25 -p tcp -j ACCEPT \
\
iptables -I FORWARD 1 -s 192.168.30.0/24 -j DROP\
iptables -I FORWARD 1 -d 192.168.30.0/24 -j DROP
\
\

#pc
ifconfig eth0 50.50.0.100 netmask 255.255.255.128 up\
ip route add default via 50.50.0.3\

\
set nat source rule 100 outbound-interface 'eth1
\
set nat source rule po source address 40.40.0.2/24
\
set nat source rule 100 translation address' masquerade
\

# router
ifconfig eth0 10.10.0.1 netmask 255.255.255.0 up\
\
ifconfig eth1 30.30.0.1 netmask 255.255.0.0 up\
\
ifconfig eth2 20.20.0.1 netmask 255.255.240.0 up\
\

ip route add 40.40.0.0/24 via 30.30.0.2\
ip route add 50.50.0.0/25 via 20.20.0.3\

#lab.conf
\pc1[0]=A
\
pc1[image]="unibaktr/alpine:busybox"
\
pc2[0]=A
\
pc2[image]="unibaktr/alpine:busybox"
\
r1[0]=A
\
r1[1]=B
\
r1[2]=C
\
r1[image]="unibaktr/vyos"
\
\
#load blancing
iptables --table nat --append PREROUTING --destination 100.0.0.2 -p tcp --dport 80 --match statistic --mode random --probability 0.33 --jump DNAT --to-destination 10.0.0.1:80
\
iptables --table nat --append PREROUTING --destination 100.0.0.2 -p tcp --dport 80 --match statistic --mode random --probability 0.5 --jump DNAT --to-destination 10.0.0.2:80
\
iptables --table nat --append PREROUTING --destination 100.0.0.2 -p tcp --dport 80 --jump DNAT --to-destination 10.0.0.3:80
\
#dhcp \
\
configure
\
 set service dhcp-server shared-network-name dhcpexample authoritative enable 
 \
 set service dhcp-server shared-network-name dhcpexample subnet 10.10.0.0/24 default-router 10.10.0.1
 \
 set service dhcp-server shared-network-name dhcpexample subnet 10.10.e.0/24 start 10.16.0.100 stop 18.10.0.200
