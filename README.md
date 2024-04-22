#tacacs
<img width="533" alt="tacas server config" src="https://github.com/chaitanyaodd1/packet-tracer/assets/123499268/5605c0f1-0326-471d-8849-0b493ef4949a">




NAT
STATIC
after assigning ips

R1
ip nat inside source static 192.168.30.2 10.0.0.3

int Fa0/0
ip nat inside
int Se1/0
i nat outside
exit
ip route 0.0.0.0 0.0.0.0 Serial1/0

do sh ip nat tra


DYNAMIC

after assigning ips

access-list 10 permit 192.168.30.0 0.0.0.255
ip nat pool ditiss 10.0.0.4 10.0.0.6 netmask 255.255.255.0
ip nat inside source list 10 pool ditis
int Fa0/0
ip nat inside
int Se1/0
ip nat outside
ip route 0.0.0.0 0.0.0.0 serial1/0

do sh ip nat tra

PAT

after assigning ips 

access-list 10 permit 192.168.30.0 0.0.0.255
ip nat pool ditiss 10.0.0.4 10.0.0.4 netmask 255.255.255.0
ip nat inside source list 10 pool ditis overload
int Fa0/0
ip nat inside
int Se1/0
ip nat outside
ip route 0.0.0.0 0.0.0.0 serial1/0

TACACS+

switch(config)#interface vlan 1
ip address 192.168.0.2 255.255.255.0

aaa new-model
username ditiss password ayush
aaa authentication login default group tacas+ local
tacacs-server host 192.168.0.1 key xyz
line vty 0 4
login authentication default
aaa authorization exec fedault group tacas+
aaa authorization exec default group tacas+ local
do wr

vlan 
no shut

go to services in server and entry in AAA 
then try telent from authorized PC to telnet 192.168.10.2

RADIUS
username admin3 secret admin3pa55
radius-server host 192.168.3.2
radius-server key radiuspa55
aaa new-model
aaa authentication login default group radius local
line console 0
login authentication default

ACL

STANDARD
closest to R3

router (configure)# access-list 1- deny host 192.168.10.2
access-list 10 permit any
do wr

APPly to port

int Fa0/0
ip access-group 10 out
do wr

FOR NETWORK
access-list 10 deny 192.168.10.0 0.0.0.255

EXTENDED
mearest source R1

router(confugure) # acess-list 100 deny tcp host 192.168.10.2 host 192.168.50.2 eq telnet
access-list 100 deny tcp host 192.168.10.2 host 192.168.30.1 eq 23
access-list 100 permit ip any any

int fa0/0
ip access-group 100 in 
do wr


RE-DISTRIBUTION

R1 eigrp 

R1(config)# router eigrp 12
no auto-summary
network 192.168.10.0
network 192.168.20.0
R3
#router rip
version 2
no auto-summary
network 192.168.40.0
network 192.168.50.0

R2#
router eigrp 12
no auto-summary
network 192.168.20.0

router rip
no auto-summary
version 2
network 192.168.40.0

router eigrp 12
#redistribution rip metric 1500 100 255 1 1500
router rip
redistribute eigrp 12 metric 1



 DHCP from r3 router :

R3:
ip dhcp pool xxxx
network xxxxxx
default-router fa0/0 gateway R3
ip dhcp pool xxxx
network xxxxxx
default-router fa0/0 gateway R2
ip dhcp pool xxxx
network xxxxxx
default-router fa0/0 gateway R1

RELAY AGENT

R2#
int Fa0/0
ip helper-address (r3Se1/0 default gateway)

R3#
int Fa0/0
ip helper_address (r3Se1/0 default gateway)

