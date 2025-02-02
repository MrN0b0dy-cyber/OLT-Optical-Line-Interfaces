#Comandos gerais
>show running-configuration /it displays OLT all config

## Consult service ports
> Edit
## Create a profile 
> Edit

## Not provisioned > show interface gpon discovered-onus
List ONU on inteface:

> show interface gpon 1/X/X onu


## Provisioning an ONU on bridge mode:
     *Consultar antes um numero de ID para a porta que não exista e service port
     *obs: remove the ""
     
     conf t
     interface gpon 1/1/5
     onu ID
     name "Description"
     serial-number DACM00xxxxxx
     line-profile NAME_LINE_PROFILE
     snmp profile SNMP_profile
     ethernet 1
     negotiation
     no shutdown
     exit
     exit
     service-port xxxx gpon 1/1/5 onu ID gem 1 match vlan vlan-id X action vlan replace vlan-id X
     service-port xxxx gpon 1/1/5 onu ID gem 2 match vlan vlan-id X action vlan replace vlan-id X

--

## Provisioning an ONU on router mode: 
> edit
