---
title: Översikt över Azure App Security-grupper
titlesuffix: Azure Virtual Network
description: Lär dig mer om användningen av program säkerhets grupper.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4d8ffe8451b2b2a08ab30761eaf3a928b5e117b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537636"
---
# <a name="application-security-groups"></a>Programsäkerhetsgrupper

Med programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper. Du kan återanvända din säkerhetsprincip i stor skala utan manuellt underhåll av explicita IP-adresser. Plattformen hanterar komplexiteten med explicita IP-adresser och flera regeluppsättningar så att du kan fokusera på affärslogik. Följande exempel hjälper dig att bättre förstå programsäkerhetsgrupper:

![Programsäkerhetsgrupper](./media/security-groups/application-security-groups.png)

I föregående bild är *NIC1* och *NIC2* medlemmar i programsäkerhetsgruppen *AsgWeb*. *NIC3* är medlem i programsäkerhetsgruppen *AsgLogic*. *NIC4* är medlem i programsäkerhetsgruppen *AsgDb*. Även om varje nätverks gränssnitt i det här exemplet bara är medlem i en nätverks säkerhets grupp, kan ett nätverks gränssnitt vara medlem i flera program säkerhets grupper, upp till [Azure-gränserna](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Inget av nätverksgränssnitten har en associerad nätverkssäkerhetsgrupp. *NSG1* är associerat med båda undernäten och innehåller följande regler:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Den här regeln krävs för att tillåta trafik från Internet till webbservrarna. Eftersom inkommande trafik från Internet nekas av standardsäkerhetsregeln **DenyAllInbound**, krävs ingen ytterligare regel för programsäkerhetsgruppen *AsgLogic* eller *AsgDb*.

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Tillåt |

## <a name="deny-database-all"></a>Deny-Database-All

Eftersom standardsäkerhetsregeln **AllowVNetInBound** tillåter all kommunikation mellan resurser i samma virtuella nätverk, krävs den här regeln för att neka trafik från alla resurser.

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Valfri | Neka |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Den här regeln tillåter trafik från programsäkerhetsgruppen *AsgLogic* till programsäkerhetsgruppen *AsgDb*. Den här regeln har högre prioritet än regeln *Deny-Database-All*. Det innebär att den här regeln bearbetas före regeln *Deny-Database-All*, så att trafik från programsäkerhetsgruppen *AsgLogic* tillåts, medan all annan trafik blockeras.

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Tillåt |

Reglerna som definierar en programsäkerhetsgrupp som källan eller målet tillämpas bara på nätverksgränssnitt som är medlemmar i programsäkerhetsgruppen. Om nätverksgränssnittet inte är medlem i en programsäkerhetsgrupp tillämpas inte regeln på nätverksgränssnittet, även om nätverkssäkerhetsgruppen är associerad med undernätet.

Programmet säkerhetsgrupper har följande begränsningar:

-    Det finns gränser för hur många programsäkerhetsgrupper du kan ha i en prenumeration, liksom andra begränsningar som gäller för programsäkerhetsgrupper. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Du kan ange en programsäkerhetsgrupp som källa och mål i en säkerhetsregel. Du kan inte ange flera programsäkerhetsgrupper i källan eller målet.
- Alla nätverksgränssnitt som har tilldelats till en programsäkerhetsgrupp måste finnas i samma virtuella nätverk som det första nätverksgränssnittet som programsäkerhetsgruppen finns i. Om exempelvis det första nätverksgränssnittet som tilldelats till en programsäkerhetsgrupp som heter *AsgWeb* finns i det virtuella nätverket med namnet *VNet1*, måste alla efterföljande nätverksgränssnitt som tilldelats *AsgWeb* finnas i *VNet1*. Du kan inte lägga till nätverksgränssnitt från olika virtuella nätverk i säkerhetsgruppen för samma program.
- Om du anger en programsäkerhetsgrupp som källa och mål i en säkerhetsregel, måste nätverksgränssnitten i bägge programsäkerhetsgrupperna finnas i samma virtuella nätverk. Om till exempel *AsgLogic* innehåller nätverksgränssnitt från *VNet1*, och *AsgDb* innehåller nätverksgränssnitt från *VNet2*, kan du inte tilldela *AsgLogic* som källan och *AsgDb* som målet i en regel. Alla nätverksgränssnitt för både käll- och målprogramsäkerhetsgrupperna måste finnas i samma virtuella nätverk.

> [!TIP]
> För att minimera antalet säkerhetsregler, och behovet av att behöva ändra reglerna, bör du tänka igenom vilka programsäkerhetsgrupper du behöver och sedan skapa regler med hjälp av tjänsttaggar eller programsäkerhetsgrupper, i stället för att använda enskilda IP-adresser eller IP-adressintervall.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [skapar en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md).
