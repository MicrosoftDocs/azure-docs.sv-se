---
title: 'Arbeta fjärran slutet: NVA (Network Virtual installation) för fjärran sluten arbete | Azure-VPN Gateway'
description: Den här artikeln hjälper dig att förstå de saker som du bör tänka på när du arbetar med virtuella nätverks installationer (NVA) i Azure under COVID-19-Pandemic.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 70b5732e1293e35127a19fbe736d8562056a870b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499687"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Arbeta fjärran slutet: NVA (Network Virtual installation) för fjärrarbete

>[!NOTE]
>Den här artikeln beskriver hur du kan använda virtuella nätverk för virtuella datorer, Azure, Microsoft Network och Azure-partnerns eko system för att arbeta med fjärrnätverket och minimera nätverks problem som du står inför på grund av COVID-19-kris.
>

Vissa Azure-kunder använder virtuella nätverk från tredje part (NVA) från Azure Marketplace för att tillhandahålla kritiska tjänster som punkt-till-plats-VPN för de anställda som arbetar hemifrån under COVID-19-epidemin. Den här artikeln beskriver en rad rikt linjer som du bör ta hänsyn till när du utnyttjar NVA i Azure för att tillhandahålla lösningar för fjärråtkomst.

## <a name="nva-performance-considerations"></a>NVA prestanda överväganden

Alla större NVA-leverantörer i Azure Marketplace bör ha rekommendationer om storleken på den virtuella datorn och antalet instanser som ska användas för att distribuera lösningar.  Även om nästan alla NVA-leverantörer låter dig välja vilken storlek som helst som är tillgänglig i en specifik region, är det mycket viktigt att du följer leverantörs rekommendationerna för storleken på Azure VM-instanser, eftersom dessa rekommendationer är de VM-storlekar som leverantören har slutfört prestanda testning med i Azure.  

### <a name="consider-the-following"></a>Tänk på följande

- **Kapacitet och antal samtidiga användare** – det här talet är särskilt viktigt för punkt-till-plats-VPN-användare som varje ansluten användare skapar en krypterad (IPSec-eller SSL VPN)-tunnel.  
- **Sammanställd genom strömning** – vilken mängd bandbredd du behöver för att hantera antalet användare du behöver för att kunna tillhandahålla fjärråtkomst.
- **Den VM-storlek du behöver** – du bör alltid använda VM-storlekar som rekommenderas av NVA-leverantören.  Om du har ett stort antal samtidiga användar anslutningar för punkt-till-plats-VPN bör du använda större VM-storlekar som [Dv2 och DSv2-seriens](../virtual-machines/dv2-dsv2-series.md "Dv2 och Dsv2-serien") virtuella datorer. De här virtuella datorerna tenderar att ha fler virtuella processorer och kan hantera fler samtidiga VPN-sessioner.  Förutom att ha fler virtuella kärnor har större VM-storlekar i Azure mer sammanställd bandbredds kapacitet än mindre VM-storlekar.
    > **Viktigt:** Varje leverantör använder resurser på olika sätt.  Om det är oklart vilka instans storlekar du ska använda för att hantera den uppskattade användar belastningen bör du kontakta program leverantören direkt och be dem om en rekommendation.
- **Antal instanser** – om du förväntar dig att ha ett stort antal användare och anslutningar, finns det gränser för hur stora NVA-instanser kan uppnå.  Överväg att distribuera flera virtuella dator instanser.
- **IPSec VPN vs SSL VPN** -i allmänna IPSec-implementeringar av IPSec fungerar bättre än SSL VPN-implementeringar.  
- **Licensiering** – kontrol lera att de program varu licenser som du har köpt för NVA-lösningen kommer att ta upp den plötsliga tillväxt som kan uppstå under COVID-19-epidemin.  Många NVA-licensierings program begränsar antalet anslutningar eller bandbredd som lösningen kan hantera.
- **Accelererat nätverk** – Överväg en NVA-lösning som har stöd för accelererat nätverk.  Accelererat nätverk möjliggör Single Root I/O-virtualisering (SR-IOV) till en virtuell dator, vilket avsevärt förbättrar nätverkets prestanda. Den här högpresterande sökvägen kringgår värden från data Sök vägen, minskar svars tiden, Darr och processor belastningen för användning med de mest krävande nätverks belastningarna på VM-typer som stöds. Accelererat nätverk stöds i de flesta generella syftes-och beräknings optimerade instans storlekar med två eller flera virtuella processorer.

## <a name="monitoring-resources"></a>Övervaka resurser

Varje NVA-lösning har sina egna verktyg och resurser för att övervaka prestanda för deras NVA.  Läs leverantörens dokumentation och se till att du förstår prestanda begränsningarna och kan upptäcka när NVA är nära eller når kapacitet.  Förutom detta kan du titta på Azure Monitor nätverks insikter och se grundläggande prestanda information om dina virtuella nätverks enheter, till exempel:

- CPU-användning
- Nätverk – inkommande
- Nätverk – utgående
- Inkommande flöden
- Utgående flöden

## <a name="next-steps"></a>Nästa steg

De flesta större NVA-partners har publicerat vägledning kring skalning för plötslig, oväntad tillväxt under COVID-19. Här är några användbara länkar till partner resurser.

[Barracuda gör det möjligt att arbeta hemifrån samtidigt som du skyddar dina data under COVID-19](https://www.barracuda.com/covid-19/work-from-home "Aktivera arbete från hemmet när du skyddar dina data under COVID-19")

[Check Point skydda fjärran sluten personal under coronavirus](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Skydda fjärran sluten personal under coronavirus")

[Cisco AnyConnect-implementering och prestanda-/skalnings referens för COVID-19-förberedelse](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect-implementering och prestanda-/skalnings referens för COVID-19-förberedelse")

[Citrix COVID-19 svars Support Center](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 svars Support Center")

[F5-vägledning för att lösa dramatisk ökningen i fjärranslutna arbetare](https://www.f5.com/business-continuity "F5-vägledning för att lösa dramatisk ökningen i fjärranslutna arbetare")

[Fortinet COVID-19 uppdateringar för kunder och partner](https://www.fortinet.com/covid-19.html "COVID-19 uppdateringar för kunder och partner")

[Palo-nätverk COVID-19-svars Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo-nätverk COVID-19-svars Center")

[Kemp: Aktivera fjärran sluten arbete och Always-On app Experience för affärs kontinuitet](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "Kemp: Aktivera fjärran sluten arbete och Always-On app Experience för affärs kontinuitet")

