# Comandos Gerais:

## Nomear OLT
>hostname OLT_NOME_X

## Ver conf total
>show running-config


## Ver tipos de ONU CONFIGURADOS
>show running-config | include onu-type 

Obs*: usar derivados pare filtrar

## Não provisionadas
>show pon onu uncfg 

## Descobrir ONU por serial
>show gpon onu by sn ZTEXXXXX

## Ver configurações de ONU ON ZTE C300 family

    $Primeira parte da conf
    >show running-config interface gpon-onu_1/S/P:ID
    	
    $Segunda parte da conf
    >show onu running config gpon-onu_1/S/P:ID

    $Ver ID's na pon
    >show gpon onu baseinfo gpon-onu_1/S/P:ID


    #Ver sinal
    >show pon power attenuation gpon-onu_1/S/P:ID


# Ver IP DA ONU
>show gpon remote-onu ip-host gpon-onu_1/S/P:ID



## Interface vlan na OLT:

    interface vlan 2000
      ip address 10.220.155.1 255.255.255.0
    


## Passar vlans na porta:

    interface xgei_1/x/x
      phy-attribute lan
      no shutdown
      hybrid-attribute fiber
      no negotiation auto
      speed 10000
      duplex full
      flowcontrol disable
      linktrap enable
      description xgei_1/19/1
      switchport mode trunk
      switchport vlan 1,104,30-40,84 tag
      port-protect disable
      uplink-isolate disable



#Rotas

  ip route 0.0.0.0 0.0.0.0 IP_GATEWAY

  ou

  ip route DESTINO  IP_GATEWAY




#SNMP


  snmp-server location Mato Grosso
  snmp-server contact +55 XXXXXXXXX
  snmp-server packetsize 8192
  snmp-server engine-id mode mac
  snmp-server group GroupPriv15 v3 priv read AllView write AllView
  snmp-server group GroupPriv10 v3 priv read AllView write ViewPriv10
  snmp-server group GroupPriv5 v3 priv read AllView write ViewPriv5
  snmp-server group GroupPriv0 v3 priv read AllView
  snmp-server community prtggpon view AllView ro
  snmp-server community community_NAME view allview rw
  snmp-server view AllView 1.2 included
  snmp-server view AllView 1.3 included
  snmp-server view allview 1.3 included
  snmp-server view ViewPriv5 1.2 included
  snmp-server view ViewPriv5 1.3 included
  snmp-server view ViewPriv5 1.3.6.1.4.1.3902.1082.10.1 excluded
  snmp-server view ViewPriv5 1.3.6.1.4.1.3902.1082.10.10 excluded
  snmp-server view ViewPriv5 1.3.6.1.4.1.3902.1082.20.1 excluded
  snmp-server view ViewPriv5 1.3.6.1.4.1.3902.1082.20.10 excluded
  snmp-server view ViewPriv10 1.2 included
  snmp-server view ViewPriv10 1.3 included
  snmp-server view ViewPriv10 1.3.6.1.4.1.3902.1082.20.10 excluded



#Configurar NTP

  ntp server IP_NTP_SERVER priority 1
  ntp enable
  ntp client








## ZTE C300 MODE:

      #Configuração bridge com duas Vlans:


      enable
      conf t
      interface gpon-olt_1/S/P
      onu ID type XXXX sn SERIAL_NUMBER
      exit
      interface gpon-onu_1/S/P:ID
      name DESCRIPTION
      tcont 1 profile PROFILE_NAME     
      gemport 1 tcont 1
      service-port 1 vport 1 user-vlan VLAN_ID vlan VLAN_ID
      service-port 2 vport 1 user-vlan VLAN_ID vlan VLAN_ID 
      exit
      pon-onu-mng gpon-onu_1/S/P:ID
      name DESCRIPTION
      service 1 gemport 1 vlan VLAN_ID
      service 2 gemport 1 vlan VLAN_ID 
      vlan port eth_0/1 mode transparent  #Faz a conf só para porta 1
      security-mgmt 1 state enable ingress-type lan protocol web 
      wifi disable
      end
      write
      !


      #Configuração ROUTER


      conf t
      interface gpon-olt_1/S/P
      onu ID type F680 sn SERIAL_NUMBER
      exit
      interface gpon-onu_1/S/P:ID
      description DESCRIPTION
      sn-bind enable sn
      tcont 1 name INTERNET profile PROFILE_NAME 
      tcont 1 gap mode2
      gemport 1 name INTERNET tcont 1
      switchport mode hybrid vport 1
      service-port 1 vport 1 user-vlan VLAN_ID vlan VLAN_ID
      end
      conf t
      pon-onu-mng gpon-onu_1/S/P:ID
      switchport-bind switch_0/1 iphost 1
      switchport-bind switch_0/1 veip 1
      service INTERNET gemport 1 vlan VLAN_ID
      name DESCRIPTION
      pppoe 1 nat enable release-timer internal user PPPOE_USERNAME password PPPOE_PASSWORD
      wan 1 ethuni 1-4 ssid 1 service internet
      vlan wan 1 mode tag vlan VLAN_ID
      vlan-filter-mode iphost 1 tag-filter vlan-filter untag-filter discard
      vlan-filter iphost 1 pri 0 vlan VLAN_ID
      security-mgmt 1 state enable ingress-type lan protocol web   #Libera WEB
      security-mgmt 2 state enable mode forward protocol web       #Libera WEB
      security-mgmt 2 start-src-ip x.x.x.x end-src-ip x.x.x.254 #LIBERA
      security-mgmt 3 state enable mode forward protocol web
      security-mgmt 3 start-src-ip x.x.x.x end-src-ip x.x.x.254  #LIBERA
      firewall enable level low
      end



## ZTE C600(TITAN) MODE:

#Configuração bridge com duas Vlans:  

    enable
    conf t
    interface gpon_olt-1/S/P
    onu ID type F680 sn SERIAL_NUMBER
    exit
    interface gpon_onu-1/S/P:ID
    name PPPOE_USERNAME
    tcont 1 profile PROFILE_NAME  #PROFILE_NAME
    gemport 1 tcont 1
    exit
    interface vport-1/S/P.ID:1
    service-port 1 user-vlan VLAN_ID vlan VLAN_ID
    service-port 2 user-vlan VLAN_ID vlan VLAN_ID
    exit
    pon-onu-mng gpon_onu-1/S/P:ID
    name PPPOE_USERNAME
    service 1 gemport 1 vlan VLAN_ID
    service 2 gemport 1 vlan VLAN_ID 
    vlan port eth_0/1 mode transparent
    security-mgmt 1 state enable ingress-type lan protocol web 
    wifi disable
    end
    write



    #Configuração ROUTER

    configure terminal
    interface gpon_olt-1/S/P
    onu ID type F6600 sn SERIAL_NUMBER
    exit
    interface gpon_onu-1/S/P:ID
    description PPPOE_USERNAME
    name PPPOE_USERNAME
    sn-bind enable sn
    tcont 1 name INTERNET profile PROFILE_NAME
    tcont 1 gap mode2
    gemport 1 name INTERNET tcont 1
    exit
    interface vport-1/S/P.ID:1
    service-port 1 user-vlan VLAN_ID vlan VLAN_ID
    exit
    pon-onu-mng gpon_onu-1/S/P:ID
    service INTERNET gemport 1 vlan VLAN_ID
    wan-ip ipv4 mode pppoe username PPPOE_USERNAME password PPPOE_PASSWORD vlan-profile VLAN_ID host 1
    security-mgmt 1 state enable ingress-type lan protocol web #Libera WEB
    security-mgmt 2 state enable mode forward protocol web     #Libera WEB
    security-mgmt 2 start-src-ip 177.67.192.0 end-src-ip 177.67.192.254 #libera prefix
    security-mgmt 3 state enable mode forward protocol web
    security-mgmt 3 start-src-ip 10.0.0.0 end-src-ip 10.254.254.254     #libera prefix
    security-mgmt 4 state enable mode forward protocol web
    security-mgmt 4 start-src-ip 177.36.32.1 end-src-ip 177.36.47.254   #libera prefix
    firewall enable level low
    end
    write
