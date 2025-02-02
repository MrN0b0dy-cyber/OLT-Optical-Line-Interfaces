
# Comandos Gerais
## Nomear a OLT
>sysname NOME_OLT


## Ver configurações da OLT
>display current-configuration


## Ver log ONU
>config
>inteface gpon FRAME/SLOT
>display ont register-info PORTA ID


## Ver não provisionadas
>display ont autofind all


## Ver ID's na pon 
>display ont F S P all

## Ver ONU pelo serial
>display ont info by-sn SERIAL	



## Ver profiles
>display ont-lineprofile gpon all
>display ont-srvprofile gpon all


## Configurar profiles

Profile com 2 vlan 50 e 30 passando em todas portas da ONU:

adicionar badnwidth controle

>dba-profile add profile-id 100 profile-name "Profile-1G" type4 max 1024000


ont-srvprofile gpon profile-id 50 profile-name "NOME_PROFILE"
	  ont-port pots adaptive 32 eth adaptive 8
	  port vlan eth 1 translation 50 user-vlan 50
	  port vlan eth 1 translation 30 user-vlan 30
	  port vlan eth 2 translation 50 user-vlan 50
	  port vlan eth 2 translation 30 user-vlan 30
	  port vlan eth 3 translation 50 user-vlan 50
	  port vlan eth 3 translation 30 user-vlan 30
	  port vlan eth 4 translation 50 user-vlan 50
	  port vlan eth 4 translation 30 user-vlan 30
	  commit
	  quit


>ont-lineprofile gpon profile-id 50 profile-name "NOME_LINE_PROFILE"
	  tcont 4 dba-profile-id 100
	  gem add 50 eth tcont 4
	  gem add 30 eth tcont 4
	  gem mapping 50 0 vlan 50
	  gem mapping 30 0 vlan 30
	  commit
	  quit



## Criar VLAN 

>vlan 30 smart 
>vlan name 30 VLAN_DADOS_30
>commit


## Passar vlan na portas

>port vlan 30 0/6 0   #Frame/Slot PORTA
>commit




## Provisionar ONU:

### ROUTER/PPPoE autenticando na ONU por vlan:

	LEGENDA: 
	F: Frame 
	S: Slot 
	P: Port
	ID: ONT POSIÇÃO. 

	display ont autofind all
	interface gpon F/S
	display ont info P all
	display ont info F S P all
	interface gpon F/S
	ont add P ID sn-auth "SERIAL_NUMBER" omci ont-lineprofile-id 50 ont-srvprofile-id 50 desc "CLIENT_XXXX"              
	ont ipconfig P ID pppoe vlan 50 priority 0 user-account username pppoe_username password pppoe_password
	quit
	service-port vlan 50 gpon F/S/P ont ID gemport 90 multi-service user-vlan 50 tag-transform translate




### MODO BRIDGE: 

	LEGENDA: 
	F: Frame 
	S: Slot 
	P: Port
	ID: ONT POSIÇÃO. 

	display onf autofind all
	config
	interface gpon F/S
	ont add P ID sn-auth "SERIAL_NUMBER" omci ont-lineprofile-id 50 ont-srvprofile-id 50 desc "CLIENT_XXXX"
	quit
	service-port vlan 50 gpon F/S/P ont ID gemport 50 multi-service user-vlan 50 tag-transform transparent
	service-port vlan 30 gpon F/S/P ont ID gemport 30 multi-service user-vlan 30 tag-transform transparent
