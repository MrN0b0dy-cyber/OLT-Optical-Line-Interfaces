
 ██████╗ ██╗  ████████╗    ██████╗  █████╗ ████████╗ █████╗  ██████╗ ██████╗ ███╗   ███╗
██╔═══██╗██║  ╚══██╔══╝    ██╔══██╗██╔══██╗╚══██╔══╝██╔══██╗██╔════╝██╔═══██╗████╗ ████║
██║   ██║██║     ██║       ██║  ██║███████║   ██║   ███████║██║     ██║   ██║██╔████╔██║
██║   ██║██║     ██║       ██║  ██║██╔══██║   ██║   ██╔══██║██║     ██║   ██║██║╚██╔╝██║
╚██████╔╝███████╗██║       ██████╔╝██║  ██║   ██║   ██║  ██║╚██████╗╚██████╔╝██║ ╚═╝ ██║
 ╚═════╝ ╚══════╝╚═╝       ╚═════╝ ╚═╝  ╚═╝   ╚═╝   ╚═╝  ╚═╝ ╚═════╝ ╚═════╝ ╚═╝     ╚═╝
                                                                                        v1



Nao provisionadas > show interface gpon discovered-onus
Listar ONUS >> OLT# show interface gpon 1/X/X onu


interface gpon 1/1/5
onu 19
name ALG-PANVEL-PV50-TAGYU2ZL7TEV 
serial-number DACM000120A8
line-profile PPPOE-AVATO
snmp profile GPON-SNMP
ethernet 1
negotiation
no shutdown
!
service-port 10005 gpon 1/1/5 onu 19 gem 1 match vlan vlan-id 4000 action vlan replace vlan-id 4000
service-port 10006 gpon 1/1/5 onu 19 gem 2 match vlan vlan-id 3999 action vlan replace vlan-id 3999

--


VALIDADO: 



interface gpon 1/1/2
 onu 14
  name COOP-CRED-TAGCMTE2AYRQ
  serial-number DACM00007C04
  line-profile PPPOE-AVATO-VLAN500
  ethernet 1
   no negotiation
   no shutdown
  !
 !
 
service-port 56566 gpon 1/1/2 onu 14 gem 1 match vlan vlan-id 500 action vlan replace vlan-id 500
service-port 56567 gpon 1/1/2 onu 14 gem 2 match vlan vlan-id 3999 action vlan replace vlan-id 3999
