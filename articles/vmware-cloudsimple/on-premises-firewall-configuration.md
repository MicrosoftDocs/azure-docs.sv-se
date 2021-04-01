---
title: Få åtkomst till Azure VMware-lösningen från CloudSimple lokalt
titleSuffix: Azure VMware Solution by CloudSimple
description: Få åtkomst till din Azure VMware-lösning från CloudSimple från ditt lokala nätverk via en brand vägg
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6ff057d99e29c7c6fe30e77f38a0bff265dbe7bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86998907"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Åtkomst till din CloudSimple privata moln miljö och program från lokala platser

En anslutning kan konfigureras från det lokala nätverket till CloudSimple med hjälp av Azure ExpressRoute eller plats-till-plats-VPN.  Få åtkomst till din CloudSimple privata moln vCenter och alla arbets belastningar som du kör i det privata molnet med hjälp av anslutningen.  Du kan styra vilka portar som ska öppnas på anslutningen med hjälp av en brand vägg i ditt lokala nätverk.  I den här artikeln beskrivs några av de typiska programmens port krav.  För andra program finns det information om Port krav i program dokumentationen.

## <a name="ports-required-for-accessing-vcenter"></a>Portar som krävs för åtkomst till vCenter

För att få åtkomst till ditt privata moln vCenter-och NSX-chef måste portarna som definieras i tabellen nedan öppnas i den lokala brand väggen.  

| Port       | Källa                           | Mål                      | Syfte                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Lokala DNS-servrar          | DNS-servrar för privata moln        | Krävs för att vidarebefordra DNS-sökning av *AZ.cloudsimple.io* till privata MOLNETS DNS-servrar från det lokala nätverket.       |
| 53 (UDP)   | DNS-servrar för privata moln        | Lokala DNS-servrar          | Krävs för att vidarebefordra DNS-sökning efter lokala domän namn från privata moln vCenter till lokala DNS-servrar. |
| 80 (TCP)   | Lokalt nätverk              | Nätverk för hantering av privata moln | Krävs för omdirigering av vCenter-URL från *http* till *https*.                                                           |
| 443 (TCP)  | Lokalt nätverk              | Nätverk för hantering av privata moln | Krävs för åtkomst till vCenter och NSX-T Manager från det lokala nätverket.                                             |
| 8000 (TCP) | Lokalt nätverk              | Nätverk för hantering av privata moln | Krävs för vMotion av virtuella datorer från lokal plats till privat moln.                                            |
| 8000 (TCP) | Nätverk för hantering av privata moln | Lokalt nätverk              | Krävs för vMotion av virtuella datorer från privat moln till lokalt.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portar som krävs för att använda lokala Active Directory som identitets källa

Om du vill konfigurera lokal Active Directory som identitets källa i privat moln-vCenter, måste portarna som definierats i tabellen öppnas.  Se [använda Azure AD som identitets leverantör för vCenter på CloudSimple privata moln](./azure-ad.md) för konfigurations steg.

| Port         | Källa                           | Mål                                         | Syfte                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | DNS-servrar för privata moln        | Lokala DNS-servrar                             | Krävs för att vidarebefordra DNS-sökning efter lokala Active Directory-domännamn från privata moln vCenter till lokala DNS-servrar.          |
| 389 (TCP/UDP) | Nätverk för hantering av privata moln | Lokala Active Directory-domänkontrollanter     | Krävs för LDAP-kommunikation från privat moln vCenter-Server till Active Directory-domänkontrollanter för användarautentisering.                |
| 636 (TCP)     | Nätverk för hantering av privata moln | Lokala Active Directory-domänkontrollanter     | Krävs för säker LDAP-kommunikation (LDAP) från privat moln vCenter-Server till Active Directory-domänkontrollanter för användarautentisering. |
| 3268 (TCP)    | Nätverk för hantering av privata moln | Lokala Active Directory-globala katalog servrar | Krävs för LDAP-kommunikation i en distribution med flera domänkontrollanter.                                                                        |
| 3269 (TCP)    | Nätverk för hantering av privata moln | Lokala Active Directory-globala katalog servrar | Krävs för LDAP-kommunikation i en distribution av flera domänkontrollanter.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Vanliga portar som krävs för att komma åt virtuella arbets belastnings datorer

Åtkomst till arbets belastnings datorer som körs i privata moln kräver att portar öppnas i den lokala brand väggen.  I tabellen nedan visas några av de vanliga portarna som krävs och deras syfte.  Information om alla programspecifika port krav finns i program dokumentationen.

| Port         | Källa                         | Mål                          | Syfte                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Lokalt nätverk            | Privat moln arbets belastnings nätverk       | Säker shell-åtkomst till virtuella Linux-datorer som körs i ett privat moln.              |
| 3389 (TCP)    | Lokalt nätverk            | Privat moln arbets belastnings nätverk       | Fjärr skrivbord till virtuella Windows-datorer som körs i ett privat moln.                 |
| 80 (TCP)      | Lokalt nätverk            | Privat moln arbets belastnings nätverk       | Åtkomst till alla webb servrar som distribueras på virtuella datorer som körs i ett privat moln.        |
| 443 (TCP)     | Lokalt nätverk            | Privat moln arbets belastnings nätverk       | Åtkomst till alla säkra webb servrar som distribueras på virtuella datorer som körs i ett privat moln. |
| 389 (TCP/UDP) | Privat moln arbets belastnings nätverk | Lokalt Active Directory-nätverk | Anslut virtuella datorer med Windows-arbetsbelastning till en lokal Active Directory-domän.       |
| 53 (UDP)      | Privat moln arbets belastnings nätverk | Lokalt nätverk                  | Åtkomst till DNS-tjänsten för virtuella arbets belastnings datorer till lokala DNS-servrar.         |

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera VLAN och undernät](./create-vlan-subnet.md)
* [Ansluta till ett lokalt nätverk med hjälp av Azure ExpressRoute](./on-premises-connection.md)
* [Konfigurera plats-till-plats-VPN från lokal plats](./vpn-gateway.md)
