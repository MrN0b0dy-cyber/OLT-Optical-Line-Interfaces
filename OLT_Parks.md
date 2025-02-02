#Comandos Gerais:

## Ver configurações exceto da GPON:
>show running-config -gpon


#Adicionar ONU na pon:
>conf t
>interface gpon1/1
>onu add serial-number prks00bxxxxxx


## Consultar Blacklist:
>show gpon blacklist

## Remover Blacklist
*Localize a interface que está com ONU duplicada, entre nela e delete
>conf t
>interface gponS/P
>no blacklist serial-number prksxxxxxx
>end

#Ver serial da ONU e informações:
>show gpon onu prks00bxxxxxx summary

#Ver Profiles na ONU e IP:
>show gpon onu prks00bxxxxxx

#Adicionar IP na ONU:
dentro de config/interface
>onu prks00bxxxxxx ip address A.B.C.D/M

#Porta com vlans em trunk

**ALERTA, toda vez que adicionar uma vlan jogar todas existentes junto, pois irá sobrescrever**

	config t
	interface gpon1/4
	 description LOCAL-X-XX
	 storm-control broadcast packets 1000
	 storm-control multicast packets 1000
	 switchport mode trunk
	 switchport trunk allowed vlan 3,42-43,75,154
	 no shutdown

#Configurar NTP:
>ntp server IP_NTP_SERVER

#Configurar SNMP:
>snmp-server community community_NOME rw
>snmp-server enable traps snmp

#Configurar profiles:
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




#Provisionar a ONU Bridge: 

	*ATIVAR PRKS*
	show gpon onu prks###### summary
	conf t 
	interface gpon//										
	onu prks##### alias NOME_CLIENTE
	onu cod-login-cto flow-profile Profile_NAME
	onu  prks#####  vlan-translation Profile_NAME uni-port  1-1  *obs: 1-1 indica as portas fisicas a passar as vlans
	end
	copy running startup-config 


#Provisionar a ONU Router: 
	
	 onu prks00xxxxx alias XXXXXXX
	 onu prks00xxxxx ethernet-profile auto-on uni-port 1-2
	 onu prks00xxxxx flow-profile NOME_FLOW
	 onu prks00xxxxx iphost 1 pppoe auth auto
	 onu prks00xxxxx iphost 1 pppoe contrigger alwayson idletimer 1200
	 onu prks00xxxxx iphost 1 pppoe nat enable
	 onu prks00xxxxx iphost 1 pppoe username LOGIN password SENHA
  	 end
	 copy running startup-config 
