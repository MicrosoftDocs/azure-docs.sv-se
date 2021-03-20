---
title: 'Azure-ExpressRoute: exempel på router-NAT – NAT'
description: Den här sidan innehåller exempel på routerkonfiguration för Cisco-och Juniper-routrar.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 01/07/2021
ms.author: duau
ms.openlocfilehash: ae0a39d65bf0f1bc5221cd5e46493c489f7630f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012673"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Konfigurations exempel för router för att konfigurera och hantera NAT

Den här artikeln innehåller exempel på NAT-konfiguration för Cisco ASA-och Juniper SRX-serie routrar när du arbetar med ExpressRoute. De här routerkonfigurationen är avsedda att användas som exempel endast för vägledning och får inte användas som de är. Du måste arbeta med din leverantör för att komma igång med lämpliga konfigurationer för nätverket.

> [!IMPORTANT]
> Exempel på den här sidan är endast avsedda för vägledning. Du måste arbeta med leverantörens försäljnings-/teknik team och din nätverks grupp för att komma igång med lämpliga konfigurationer för att uppfylla dina behov. Microsoft stöder inte problem som rör konfigurationer som anges på den här sidan. Du måste kontakta enhets leverantören för att få support ärenden.
> 
> 

* Konfigurations exempel för routern nedan gäller för Azures offentliga och Microsoft-peering. Du konfigurerar inte NAT för privat Azure-peering. Granska [ExpressRoute-peering](expressroute-circuit-peerings.md) och [ExpressRoute NAT-krav](expressroute-nat.md) för mer information.

* Du måste använda separata NAT IP-pooler för anslutning till Internet och ExpressRoute. Att använda samma NAT-IP-pool över Internet och ExpressRoute resulterar i asymmetrisk Routning och förlust av anslutning.


## <a name="cisco-asa-firewalls"></a>Cisco ASA-brandväggar
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>PAT-konfiguration för trafik från kund nätverk till Microsoft

```console
object network MSFT-PAT
  range <SNAT-START-IP> <SNAT-END-IP>


object-group network MSFT-Range
  network-object <IP> <Subnet_Mask>

object-group network on-prem-range-1
  network-object <IP> <Subnet-Mask>

object-group network on-prem-range-2
  network-object <IP> <Subnet-Mask>

object-group network on-prem
  network-object object on-prem-range-1
  network-object object on-prem-range-2

nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range
```

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>PAT-konfiguration för trafik från Microsoft till kund nätverk

**Gränssnitt och riktning:**

Käll gränssnitt (där trafiken går in i ASA): innanför mål gränssnittet (där trafiken avslutar ASA): utanför

**Inställningarna**

NAT-pool:

```console
object network outbound-PAT
    host <NAT-IP>
```

Mål server:

```console
object network Customer-Network
    network-object <IP> <Subnet-Mask>
```

Objekt grupp för kund-IP-adresser:

```console
object-group network MSFT-Network-1
    network-object <MSFT-IP> <Subnet-Mask>

object-group network MSFT-PAT-Networks
    network-object object MSFT-Network-1
```

NAT-kommandon:

```console
nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network
```


## <a name="juniper-srx-series-routers"></a>Juniper SRX-serie routrar
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. skapa redundanta Ethernet-gränssnitt för klustret

```console
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }
```

### <a name="2-create-two-security-zones"></a>2. skapa två säkerhets zoner
* Förtroende zon för internt nätverk och ej betrodd zon för externa nätverk riktade till yttre routrar
* Tilldela lämpliga gränssnitt till zonerna
* Tillåt tjänster på gränssnitten

```console
    security {
        zones {
            security-zone Trust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth0.100;
                }
            }
            security-zone Untrust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth1.100;
                }
            }
        }
    }
```


### <a name="3-create-security-policies-between-zones"></a>3. skapa säkerhets principer mellan zoner

```console
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }
```

### <a name="4-configure-nat-policies"></a>4. Konfigurera NAT-principer
* Skapa två NAT-pooler. En kommer att användas för NAT utgående trafik till Microsoft och andra från Microsoft till kunden.
* Skapa regler för att NATa varje trafik

```console
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }
```

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Konfigurera BGP för att annonsera selektiva prefix i varje riktning
Se exempel på sidan [konfigurations exempel för routning](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. skapa principer

```console
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

