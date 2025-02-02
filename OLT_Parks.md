# Comandos Gerais:

## Ver configurações exceto da GPON:
>show running-config -gpon

# Listar não provisionadas:
>show gpon unprovisioned-onu

# Adicionar ONU na pon:
	
 	conf t
	interface gpon1/1
	onu add serial-number prks00bxxxxxx


## Consultar Blacklist:
>show gpon blacklist

## Remover Blacklist
*Localize a interface que está com ONU duplicada, entre nela e delete
conf t
interface gponS/P
no blacklist serial-number prksxxxxxx
end

# Ver serial da ONU e informações:
>show gpon onu prks00bxxxxxx summary

# Ver Profiles na ONU e IP:
>show gpon onu prks00bxxxxxx

# Adicionar IP na ONU:
dentro de config/interface
>onu prks00bxxxxxx ip address A.B.C.D/M

# Porta com vlans em trunk

**ALERTA, toda vez que adicionar uma vlan jogar todas existentes junto, pois irá sobrescrever**

	config t
	interface gpon1/4
	 description LOCAL-X-XX
	 storm-control broadcast packets 1000
	 storm-control multicast packets 1000
	 switchport mode trunk
	 switchport trunk allowed vlan 3,42-43,75,154
	 no shutdown

# Configurar NTP:
>ntp server IP_NTP_SERVER

# Configurar SNMP:
>snmp-server community community_NOME rw
>snmp-server enable traps snmp

# Configurar profiles:
Controle de banda: 
	
	gpon profile bandwidth PROFILE_NOME_1GIGA
	 	traffic-type internet maximum-bandwidth 1024000


Flow profiles:
	
	gpon profile flow NOME_FLOW_PROTILE
	 add flow # NOME_FLOW_PROTILE-1            obs*: Ao usar o add flow ele já cria o nome com-1
	 add flow # NOME_FLOW_PROTILE-2
	 NOME_FLOW_PROTILE-1 encryption disable
	 NOME_FLOW_PROTILE-1 flow-type pbmp 1
	 NOME_FLOW_PROTILE-1 vlan 500 service PROFILE_NOME_1GIGA
	 NOME_FLOW_PROTILE-2 encryption disable
	 NOME_FLOW_PROTILE-2 flow-type pbmp 1
	 NOME_FLOW_PROTILE-2 vlan 30 service PROFILE_NOME_1GIGA


Vlan translation profiles:

	gpon profile vlan-translation NOME_VLAN_TRANSLATION
		 add translation trunk 500
		 add translation trunk 30




# Provisionar a ONU Bridge: 

	*ATIVAR PRKS*
	show gpon onu prks###### summary
	conf t 
	interface gpon//										
	onu prks##### alias NOME_CLIENTE
	onu cod-login-cto flow-profile Profile_NAME
	onu  prks#####  vlan-translation Profile_NAME uni-port  1-1  *obs: 1-1 indica as portas fisicas a passar as vlans
	end
	copy running startup-config 


# Provisionar a ONU Router: 
	
	 onu prks00xxxxx alias XXXXXXX
	 onu prks00xxxxx ethernet-profile auto-on uni-port 1-2
	 onu prks00xxxxx flow-profile NOME_FLOW
	 onu prks00xxxxx iphost 1 pppoe auth auto
	 onu prks00xxxxx iphost 1 pppoe contrigger alwayson idletimer 1200
	 onu prks00xxxxx iphost 1 pppoe nat enable
	 onu prks00xxxxx iphost 1 pppoe username LOGIN password SENHA
  	 end
	 copy running startup-config 



# Configurar ONU sem acesso WEB via SSH/Telnet>

	Modelos: Parks1100, 1101 , 2200 , 2201...

	Dentro da ONU criar rota para sua gerência:

	ip route rede/x gateway


        conf t
	access-policy permit
	ip nat-rule NAT_SAIDA
	ip any any change-source-to interface-address
	!
	ip conntrack size 65536
	!
	hostname DESCRIPTION_CLIENTE
	ip routing
	no ipv6 routing
	interface bridge0
	ip address 192.168.0.1/24
	ip dhcp server local
	no multicast
	!
	!
	interface virtual-ethernet0/0/0.1
	no multicast
	pppoe 1
	no shutdown
	!
	interface pppoe1
	ip address negotiated
	ip nat NAT_SAIDA out
	no multicast
	ppp authentication pap
	ppp ipcp default-route
	ppp ipcp dns-request
	ppp password PPPOE_PASSWORD
	ppp username PPPOE_USERNAME
	no shutdown
	!
	ip dhcp pool local
	default-router  192.168.0.1
	dns-server x.x.x.x
	lease 1 0 0
	network 192.168.0.2 192.168.0.254 255.255.255.0
	!
	no ip domain lookup
	ip helper nat
	ip ssh server max-connections 5
	!



	no ipv6 routing
	!
	ip access-list ACL_DENY_EXT
	deny tcp any any eq 2210
	insert 2 deny tcp any any eq 22
	insert 3 deny tcp any any eq 1022
	insert 4 deny tcp any any eq 23
	deny tcp any any eq 161 deny udp any any eq 161
	deny udp any any eq 123
	deny udp any any eq 53
	permit tcp 201.71.128.0 0.0.0.255 any
	permit udp 201.71.128.0 0.0.0.255 any
	deny tcp any any eq 5060
	deny udp any any eq 5060
	permit ip any any
	!
	interface pppoe1
	ip access-group ACL_DENY_EXT in
	!
	ip access-list ACL_DENY_INT
	deny tcp any any eq 22
	deny tcp any any eq 23
	deny tcp any any eq 2210
	permit ip any any
	!
	no aaa username root
	!
	no aaa username guest
	!
	no aaa username user
	!
	no ip http server
	!
	clock timezone BRT -4 0
	ntp server x.x.x.x
	!
	snmp-server community prtg ro
	!
	aaa username ONU_LOGIN_USER level priviledged password ONU_LOGIN_PASSWORD
	!
	ip ssh server port 22 
	!
	no ip telnet server 
	!


	##Configurando o Wi-Fi#

	interface wifi0/0 (acessar modo interface wifi)
	shutdown (desligar shutdown)
	essid "CLIENTE5G" ("Inserir nome da rede")
	security wpa/wpa2-psk encryption aes "xxxxxxx" ("Inserir senha wifi")
	no shutdown 

	#Salvar configurações
	copy running-config startup config



