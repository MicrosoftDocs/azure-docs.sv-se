---
title: Översikt över Azure nätverks säkerhets grupper
titlesuffix: Azure Virtual Network
description: Lär dig mer om nätverks säkerhets grupper. Med nätverks säkerhets grupper kan du filtrera nätverks trafik mellan Azure-resurser.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperf-fy21q1
ms.openlocfilehash: d10c10d602dcfa8f83ee56c2755d800d76516ff1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058552"
---
# <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
<a name="network-security-groups"></a>

Du kan använda en Azure-nätverks säkerhets grupp för att filtrera nätverks trafik till och från Azure-resurser i ett virtuellt Azure-nätverk. En nätverkssäkerhetsgrupp innehåller [säkerhetsregler](#security-rules) som tillåter eller nekar inkommande nätverkstrafik till, eller utgående nätverkstrafik från, flera typer av Azure-resurser. För varje regel kan du ange källa och mål, port och protokoll.

I den här artikeln beskrivs egenskaperna för en regel för nätverks säkerhets grupper, de [Standard säkerhets regler](#default-security-rules) som tillämpas och de regel egenskaper som du kan ändra för att skapa en [förstärkt säkerhets regel](#augmented-security-rules).

## <a name="security-rules"></a><a name="security-rules"></a> Säkerhets regler

En nätverkssäkerhetsgrupp kan innehålla noll regler, eller så många regler du vill, inom Azure-prenumerationens [gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Varje regel anger följande egenskaper:

|Egenskap  |Förklaring  |
|---------|---------|
|Namn|Ett unikt namn inom nätverkssäkerhetsgruppen.|
|Prioritet | Ett tal mellan 100 och 4096. Regler bearbetas i prioritetsordning. Låga tal bearbetas före höga tal eftersom låga tal har högre prioritet. När trafiken matchar en regel avbryts bearbetningen. Det innebär att regler som har lägre prioritet (högre tal) och samma attribut som regler med högre prioritet inte bearbetas.|
|Källa eller mål| Valfria, eller enskilda IP-adresser, CIDR-block (Classless Inter-Domain Routing) (t.ex. 10.0.0.0/24), tjänsttaggar eller programsäkerhetsgrupper. Om du anger en adress för en Azure-resurs anger du den privata IP-adressen som tilldelats till resursen. Nätverkssäkerhetsgrupper bearbetas efter att Azure omvandlar en offentlig IP-adress till en privat IP-adress för inkommande trafik, och innan Azure omvandlar en privat IP-adress till en offentlig IP-adress för utgående trafik. . Du kan begränsa antalet säkerhetsregler du skapar genom att ange ett intervall, en tjänsttagg eller en programsäkerhetsgrupp. Möjligheten att ange flera enskilda IP-adresser och intervall (du kan inte ange flera tjänsttaggar eller programgrupper) i en regel kallas [förhöjda säkerhetsregler](#augmented-security-rules). Förhöjda säkerhetsregler kan bara skapas i nätverkssäkerhetsgrupper som skapats genom Resource Manager-distributionsmodellen. Du kan inte ange flera IP-adresser och IP-adressintervall i nätverkssäkerhetsgrupper som skapats via den klassiska distributionsmodellen.|
|Protokoll     | TCP, UDP, ICMP, ESP, AH eller någon.|
|Riktning| Om regeln gäller för inkommande eller utgående trafik.|
|Portintervall     |Du kan ange en enskild port eller ett portintervall. Du kan till exempel ange 80 eller 10000–10005. Om du anger intervall behöver du inte skapa lika många säkerhetsregler. Förhöjda säkerhetsregler kan bara skapas i nätverkssäkerhetsgrupper som skapats genom Resource Manager-distributionsmodellen. Du kan inte ange flera portar eller portintervall i samma säkerhetsregel i nätverkssäkerhetsgrupper som skapats med den klassiska distributionsmodellen.   |
|Action     | Tillåt eller neka        |

Säkerhetsregler för nätverkssäkerhetsgrupper utvärderas baserat på prioritet med hjälp av 5-tuppelinformationen (källa, källport, mål, målport och protokoll) för att tillåta eller neka trafik. Du får inte skapa två säkerhets regler med samma prioritet och riktning. En flödespost skapas för befintliga anslutningar. Kommunikation tillåts eller nekas baserat på flödespostens anslutningsstatus. Flödesposten gör att en nätverkssäkerhetsgrupp kan vara tillståndskänslig. Om du till exempel anger en utgående säkerhetsregel till en adress via port 80, behöver du inte ange en inkommande säkerhetsregel för svar på utgående trafik. Du behöver bara ange en inkommande säkerhetsregel om kommunikationen initieras externt. Även det motsatta gäller. Om inkommande trafik tillåts via en port, behöver du inte ange en utgående säkerhetsregel för svar på trafik via porten.

Befintliga anslutningar kanske inte avbryts när du tar bort en säkerhetsregel som aktiverade flödet. Trafikflöden avbryts när anslutningar har stoppats och ingen trafik passerar i endera riktning under minst ett par minuter.

Det finns gränser för hur många säkerhetsregler du kan skapa i en nätverkssäkerhetsgrupp. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a><a name="default-security-rules"></a> Standard säkerhets regler

Azure skapar följande standardregler i varje nätverkssäkerhetsgrupp som du skapar:

#### <a name="inbound"></a>Inkommande

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Valfri|Tillåt|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Valfri|Tillåt|

##### <a name="denyallinbound"></a>DenyAllInbound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Valfri|Neka|

#### <a name="outbound"></a>Utgående

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Valfri | Tillåt |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Valfri | Tillåt |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Valfri | Neka |

I kolumnerna **Källa** och **Mål** är *VirtualNetwork*, *AzureLoadBalancer*, och *Internet* så kallade [tjänsttaggar](service-tags-overview.md), inte IP-adresser. I kolumnen protokoll finns **alla** kompasser TCP, UDP och ICMP. När du skapar en regel kan du ange TCP, UDP, ICMP eller valfri. *0.0.0.0/0* i kolumnerna **Källa** och **Mål** representerar alla adresser. Klienter som Azure Portal, Azure CLI eller PowerShell kan använda * eller något av detta uttryck.
 
Du kan inte ta bort standardreglerna, men du kan åsidosätta dem genom att skapa regler med högre prioritet.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> Förstärkta säkerhets regler

Förhöjda säkerhetsregler förenklar säkerhetsdefinitionen för virtuella nätverk så att du kan definiera större och mer komplexa nätverkssäkerhetsprinciper med färre regler. Du kan kombinera flera portar och flera explicita IP-adresser och IP-intervall i en enda, lättbegriplig säkerhetsregel. Använd förhöjda regler i fälten för källa, mål och port för en regel. För att göra det enklare att underhålla definitionen av dina säkerhetsregler kan du kombinera förhöjda säkerhetsregler med [tjänsttaggar](service-tags-overview.md) eller [programsäkerhetsgrupper](#application-security-groups). Det finns begränsningar för antalet adresser, intervall och portar som du kan ange i en regel. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Tjänsttaggar

En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Det hjälper till att minimera komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler.

Mer information finns i avsnittet om [Azure Service-Taggar](service-tags-overview.md). Ett exempel på hur du använder taggen Storage Service för att begränsa nätverks åtkomsten finns i [begränsa nätverks åtkomsten till PaaS-resurser](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Programsäkerhetsgrupper

Med programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper. Du kan återanvända din säkerhetsprincip i stor skala utan manuellt underhåll av explicita IP-adresser. Mer information finns i [program säkerhets grupper](application-security-groups.md).

## <a name="azure-platform-considerations"></a>Azure-plattformsöverväganden

- Den **virtuella IP-adressen för noden värd**: grundläggande infrastruktur tjänster som DHCP, DNS, IMDS och hälso övervakning tillhandahålls via de virtualiserade värd-IP-adresserna 168.63.129.16 och 169.254.169.254. De här IP-adresserna tillhör Microsoft och är de enda virtualiserade IP-adresserna som används i alla regioner för det här ändamålet. Gällande säkerhets regler och effektiva vägar omfattar inte dessa plattforms regler. Om du vill åsidosätta den här grundläggande infrastruktur kommunikationen kan du skapa en säkerhets regel för att neka trafik genom att använda följande [service Taggar](service-tags-overview.md) i reglerna för nätverks säkerhets grupper: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Lär dig hur du [diagnostiserar filtrering av nätverks trafik](diagnose-network-traffic-filter-problem.md) och hur du [diagnostiserar nätverks routning](diagnose-network-routing-problem.md).
- **Licensiering (nyckelhanteringstjänsten):** Windows-avbildningar som kör på de virtuella datorerna ska vara licensierade. Licensieringen kontrolleras genom att en begäran skickas till nyckelhanteringstjänstens värdservrar som hanterar sådana frågor. Begäran är en utgående begäran via port 1688. För distributioner som använder konfigurationer med [standardflödet 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), inaktiveras denna plattformsregel.
- **Virtuella datorer i lastbalanserade pooler**: Källporten och adressintervallet som används kommer från den ursprungliga datorn, inte lastbalanseraren. Målporten och måladressutrymmet kommer från måldatorn, inte lastbalanseraren.
- **Azure-tjänstinstanser**: Instanser av flera Azure-tjänster, till exempel HDInsight, tillämpningstjänstmiljöer och VM-skalningsuppsättningar distribueras i undernät för virtuella nätverk. En fullständig lista över tjänster som du kan distribuera till virtuella nätverk finns i [Virtuellt nätverk för Azure-tjänster](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Ta reda på portkraven för varje tjänst innan du tillämpar en nätverkssäkerhetsgrupp för det undernät som resursen är distribuerad i. Om du nekar åtkomst till portar som krävs för tjänsten kommer tjänsten inte att fungera korrekt.
- **Skicka utgående e-post**: Microsoft rekommenderar att du använder AUTENTISERADE SMTP-relätjänster (vanligt vis anslutna via TCP-port 587, men även andra) för att skicka e-post från Azure Virtual Machines. SMTP-relätjänsterna är specialiserade på avsändaromdöme för att minska möjligheten att externa e-postleverantörer avvisar meddelanden. SMTP-relätjänsterna omfattar, men är inte begränsade till, Exchange Online Protection och SendGrid. Användningen av SMTP-relätjänster är inte begränsad i Azure, oavsett vilken typ av prenumeration du har. 

  Om du har skapat din Azure-prenumeration före 15 november 2017 kan du, förutom att använda SMTP-relätjänster, även skicka e-post direkt via TCP-port 25. Om du har skapat din prenumeration efter 15 november 2017 kan du inte skicka e-post direkt via port 25. Beteendet för utgående kommunikation via port 25 beror på vilken typ av prenumeration du har:

     - **Enterprise-avtal**: Utgående kommunikation via port 25 tillåts. Du kan skicka en utgående e-post direkt från virtuella datorer till externa e-postleverantörer utan begränsningar från Azure-plattformen. 
     - **Betala per användning:** Utgående kommunikation via port 25 blockeras från alla resurser. Om du behöver skicka e-post från en virtuell dator direkt till externa e-postleverantörer (inte använda ett autentiserat SMTP-relä) kan du skicka en begäran om att ta bort begränsningen. Förfrågningarna granskas och godkänns enligt Microsofts gottfinnande och beviljas endast efter att bedrägerikontroller utförts. Om du vill skicka en förfrågan öppnar du ett supportärende med ärendetypen *Teknisk*, *Virtuell nätverksanslutning*, *Det går inte att skicka e-post (SMTP/Port 25)*. I ditt supportärende anger du information om varför du (din prenumeration) behöver skicka e-post direkt till e-postleverantörer i stället för att gå via ett autentiserat SMTP-relä. Om din prenumeration undantas kan endast virtuella datorer som skapats efter undantagsdatumet använda utgående kommunikation via port 25.
     - **MSDN, Azure-pass, Azure i Open, Education, BizSpark och kostnadsfri utvärderingsversion**: Utgående kommunikation via port 25 blockeras från alla resurser. Det går inte att skicka förfrågningar om att ta bort begränsningen eftersom dessa inte beviljas. Om du vill skicka e-post från din virtuella dator måste du använda en SMTP-relätjänst.
     - **Molntjänstleverantör**: Kunder som förbrukar Azure-resurser via en molntjänstleverantör kan skapa ett supportärende hos molntjänstleverantören och begära ett avblockeringsärende om det inte går att använda ett säkert SMTP-relä.

  Även om Azure tillåter att du skickar e-post via port 25 kan Microsoft inte garantera att e-postleverantörerna godkänner inkommande e-post från din virtuella dator. Om en viss leverantör avvisar e-post från din virtuella dator kontaktar du leverantören för att lösa eventuella problem med meddelandeleverans eller skräppostfiltrering. Alternativt använder du en autentiserad SMTP-relätjänst.

## <a name="next-steps"></a>Nästa steg

* Information om vilka Azure-resurser som kan distribueras till ett virtuellt nätverk och om nätverks säkerhets grupper är kopplade till dem finns i [integrering av virtuella nätverk för Azure-tjänster](virtual-network-for-azure-services.md)
* Information om hur trafiken utvärderas med nätverks säkerhets grupper finns i [så här fungerar nätverks säkerhets grupper](network-security-group-how-it-works.md).
* Om du aldrig har skapat en nätverkssäkerhetsgrupp kan du gå en snabb [självstudie](tutorial-filter-network-traffic.md) för att få lite erfarenhet.
* Om du redan är bekant med nätverkssäkerhetsgrupper och vill lära dig hur du hanterar dem läser du [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md). 
* Om du har kommunikationsproblem och behöver felsöka nätverkssäkerhetsgrupper läser du [Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md) (Diagnostisera problem med filtreringen av nätverkstrafik för virtuella nätverk). 
* Lär dig hur du aktiverar [flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att analysera nätverks trafik till och från resurser som har en associerad nätverks säkerhets grupp.
