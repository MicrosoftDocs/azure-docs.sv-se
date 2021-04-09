---
title: Azure-regioner
description: Lär dig mer om regionerna för att köra virtuella datorer i Azure.
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthnn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.openlocfilehash: e874a76cc447158127da8482f23bf03d56961818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504617"
---
# <a name="regions-for-virtual-machines-in-azure"></a>Regioner för virtuella datorer i Azure

Det är viktigt att förstå hur och var dina virtuella datorer körs i Azure och vilka alternativ du har för att maximera prestanda, tillgänglighet och redundans. Den här artikeln ger en översikt över funktionerna för tillgänglighet och redundans i Azure.


## <a name="what-are-azure-regions"></a>Vad är Azure-regioner?
Azure körs på ett antal datacenter över hela världen. Dessa datacenter är grupperade i geografiska regioner så att du kan välja var du vill bygga dina program. 

Du skapar Azure-resurser i definierade geografiska områden som "västra USA", "Nord Europa" eller "Sydostasien". Se [listan över regioner och deras platser](https://azure.microsoft.com/regions/). I varje region finns flera datacenter för att skapa förutsättningar för redundans och tillgänglighet. Den här metoden ger dig flexibilitet när du utformar program för att skapa virtuella datorer som är närmast dina användare och som uppfyller juridiska, efterlevnads-eller skatte syfte.

## <a name="special-azure-regions"></a>Särskilda Azure-regioner
Azure har vissa särskilda regioner som du kanske vill använda när du skapar program för efterlevnad eller i juridiskt syfte. De särskilda regionerna innefattar:

* **USA Gov, Virginia** och **USA Gov, Iowa**
  * En fysisk och logisk nätverksisolerad instans av Azure för amerikanska myndigheter och partner som drivs av säkerhetskontrollerad amerikansk personal. Innefattar ytterligare efterlevnadscertifieringar som [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) och [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Läs mer om [Azure Government](https://azure.microsoft.com/features/gov/).
* **Kina, östra** och **Kina, norra**
  * Dessa regioner är tillgängliga via ett unikt partnerskap mellan Microsoft och 21Vianet, vilket innebär att Microsoft inte direkt underhåller dessa datacenter. Läs mer om [Azure Kina 21Vianet](https://www.windowsazure.cn/).
* **Tyskland, centrala** och **Tyskland, nordöstra**
  * Dessa regioner är tillgängliga via en data förvaltar modell där kund information förblir i Tyskland under kontroll av T-Systems, ett tyska telekom-företag som agerar som den tyska data förvaltar.

## <a name="region-pairs"></a>Regionpar
Varje Azure-region är kopplad till en annan region inom samma geografiska område (till exempel USA, Europa och Asien). På så sätt kan resurser som VM-lagring replikeras över geografiska områden som inte troligtvis påverkas samtidigt av naturkatastrofer, oroligheter i landet, strömavbrott eller avbrott i fysiska nätverk. Ytterligare fördelar med regionpar:

* Vid ett större Azure-avbrott prioriteras en region i varje par för att minska tiden för programåterställning. 
* Planerade Azure-uppdateringar distribueras en i taget till kopplade regioner för att minimera nedtid och risk för programavbrott.
* Data finns kvar i samma geografiska region som den andra regionen i paret (med undantag för Brasilien, södra) av skatte- och jurisdiktionsmässiga skäl.

Exempel på regionpar:

| Primär | Sekundär |
|:--- |:--- |
| USA, västra |East US |
| Europa, norra |Europa, västra |
| Sydostasien |Asien, östra |

Se [den fullständiga listan över regionpar](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Funktionstillgänglighet
Vissa tjänster eller VM-funktioner är endast tillgängliga i vissa regioner, till exempel särskilda VM-storlekar eller lagringstyper. Det finns också vissa globala Azure-tjänster som inte kräver att du väljer en viss region, till exempel [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../traffic-manager/traffic-manager-overview.md) och [Azure DNS](../dns/dns-overview.md). När du utformar din programmiljö kan du kontrollera [tillgängligheten av Azure-tjänster för varje region](https://azure.microsoft.com/regions/#services). Du kan också [program mässigt fråga efter de VM-storlekar som stöds och begränsningar i varje region](../azure-resource-manager/templates/error-sku-not-available.md).

## <a name="storage-availability"></a>Lagringstillgänglighet
Det är viktigt att förstå Azure-regioner och geografiska områden när du ska välja bland de tillgängliga alternativen för lagringsreplikering. Det finns olika replikeringsalternativ beroende på vilken lagringstyp du har.

**Azure Managed Disks**
* Lokalt redundant lagring (LRS)
  * Replikerar data tre gånger inom den region där du skapade ditt lagringskonto.

**Diskar baserade på lagringskonto**
* Lokalt redundant lagring (LRS)
  * Replikerar data tre gånger inom den region där du skapade ditt lagringskonto.
* Zonredundant lagring (ZRS)
  * Replikerar data tre gånger mellan två eller tre anläggningar, antingen inom en enda region eller mellan två regioner.
* Geo-redundant lagring (GRS)
  * Replikerar data till en sekundär region som ligger hundratals kilometer från den primära regionen.
* Geo-redundant lagring med läsbehörighet (RA-GRS)
  * Replikerar data till en sekundär region som med GRS, och ger även skrivskyddad åtkomst till data på den sekundära platsen.

Följande tabell ger en snabb översikt över skillnaderna mellan lagringsreplikeringstyperna:

| Replikeringsstrategi | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data replikeras över flera anläggningar. |Inga |Ja |Ja |Ja |
| Data kan läsas från den sekundära platsen och från den primära platsen. |Inga |Inga |Inga |Ja |
| Antal kopior av data som finns på olika noder. |3 |3 |6 |6 |

Du kan läsa mer om [Azure Storage-replikeringsalternativen här](../storage/common/storage-redundancy.md). Mer information om hanterade diskar finns i [Översikt över Azure Managed Disks](./managed-disks-overview.md).

### <a name="storage-costs"></a>Lagringskostnader
Priserna varierar beroende på vilken lagringstyp och tillgänglighet du väljer.

**Azure Managed Disks**
* Premium Managed Disks backas upp av Solid-State-enheter (SSD) och standard Managed Disks backas upp av vanliga snurrande diskar. Både Premium och Standard Managed Disks debiteras baserat på etablerad kapacitet för disken.

**Ohanterade diskar**
* Premium Storage backas upp av Solid-State-enheter (SSD) och debiteras utifrån diskens kapacitet.
* Standard Storage använder roterande diskar och debiteras baserat på den kapacitet som används och önskad lagringstillgänglighet.
  * För RA-GRS tillkommer en extra dataöverföringsavgift för geo-replikering för bandbredden som används för att replikera dessa data till en annan Azure-region.

Se [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/) för prisinformation för olika lagringstyper och tillgänglighetsalternativ.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).