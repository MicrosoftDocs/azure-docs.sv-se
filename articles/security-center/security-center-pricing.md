---
title: Azure Security Center kostnadsfri eller Azure Defender aktiverat
description: Lär dig mer om fördelarna med att aktivera Azure Defender för skydd av molnarbetsbelastningar i Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/23/2021
ms.openlocfilehash: aa65989953f761ff915383fcb59da7f36ea98dab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600490"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center kostnadsfri eller Azure Defender aktiverat
Azure Defender är kostnadsfritt under de första 30 dagarna. Om du väljer att fortsätta använda tjänsten i slutet av 30 dagar börjar vi automatiskt debitera för användning.

Du kan uppgradera från sidan **& prissättningsinställningar** enligt beskrivningen i [Snabbstart: Aktivera Azure Defender](enable-azure-defender.md). Prisinformation i den valuta du väljer och i enlighet med din region finns i [Security Center priser.](https://azure.microsoft.com/pricing/details/security-center/)

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Vilka är fördelarna med att aktivera Azure Defender?

Security Center finns i två lägen:

- **Azure Defender AV** (kostnadsfri) – Security Center utan Azure Defender aktiveras kostnadsfritt på alla dina Azure-prenumerationer när du besöker Azure Security Center-instrumentpanelen i Azure Portal för första gången, eller om den aktiveras programmässigt via API. Med det här kostnadsfria läget får du säkerhetsprinciper, kontinuerliga säkerhetsutvärderingar och säkerhetsrekommendationer som hjälper dig att skydda dina Azure-resurser.

- **Azure Defender ON** – Om du aktiverar Azure Defender utökas funktionerna i det kostnadsfria läget till arbetsbelastningar som körs i privata och andra offentliga moln, vilket ger enhetlig säkerhetshantering och skydd mot hot i dina arbetsbelastningar i hybridmoln. Några av de viktigaste funktionerna i Azure Defender:

    - **Microsoft Defender för slutpunkt –** Azure Defender för servrar innehåller Microsoft Defender för slutpunkt [för](https://www.microsoft.com/microsoft-365/security/endpoint-defender) omfattande slutpunktsidentifiering och svar (EDR). Läs mer om fördelarna med att använda Microsoft Defender för slutpunkt tillsammans med Azure Defender [i Använda Security Center integrerade EDR-lösningen](security-center-wdatp.md).
    - **Sårbarhetsgenomsökning efter virtuella** datorer och containerregister – Distribuera enkelt en skanner till alla dina virtuella datorer som tillhandahåller branschens mest avancerade lösning för sårbarhetshantering. Visa, undersöka och åtgärda resultaten direkt inom Security Center. 
    - **Hybridsäkerhet** – Få en enhetlig vy över säkerheten för alla dina lokala och molnbaserade arbetsbelastningar. Tillämpa säkerhetsprinciper och utvärdera säkerheten för dina hybridmolnarbetsbelastningar kontinuerligt för att säkerställa efterlevnad av säkerhetsstandarder. Samla in, sök efter och analysera säkerhetsdata från flera källor, inklusive brandväggar och andra partnerlösningar.
    - **Hotskyddsaviseringar** – Avancerad beteendeanalys och Microsoft Intelligent Security Graph ger en gräns över föränderliga cyberattacker. Inbyggd beteendeanalys och maskininlärning kan identifiera attacker och nolldagarskryphål. Övervaka nätverk, datorer och molntjänster för inkommande attacker och aktiviteter efter intrång. Effektivisera undersökningen med interaktiva verktyg och sammanhangsberoende hotinformation.
    - **Åtkomst- och programkontroller** (AAC) – Blockera skadlig kod och andra oönskade program genom att tillämpa maskininlärningsdrivna rekommendationer som är anpassade till dina specifika arbetsbelastningar för att skapa listor över tillåtna och nekade program. Minska nätverksangreppsytan med kontrollerad just-in-time-åtkomst till hanteringsportar på virtuella Azure-datorer. AAC minskar drastiskt exponeringen för råstyrkkraft och andra nätverksattacker.
    - **Säkerhetsfunktioner för** containrar – Dra nytta av sårbarhetshantering och skydd mot hot i realtid i dina containermiljöer. När du aktiverar Azure Defender för **containerregister** kan det ta upp till 12 timmar innan alla funktioner har aktiverats. Avgifterna baseras på antalet unika containeravbildningar som skickas till ditt anslutna register. När en bild har genomsökts en gång debiteras du inte för den igen såvida den inte ändras och push-skickas igen.
    - **Skydd mot hot** för resurser som är anslutna till Azure-miljön – Azure Defender innehåller azure-inbyggt skydd mot hot för Azure-tjänster som är gemensamma för alla dina resurser: Azure Resource Manager, Azure DNS, Azure-nätverkslager och Azure Key Vault. Azure Defender har unik insyn i Azure-hanteringslagret och Azure DNS och kan därför skydda molnresurser som är anslutna till dessa lager.


## <a name="faq---pricing-and-billing"></a>Vanliga frågor och svar – priser och fakturering 

- [Hur kan jag spåra vem i min organisation som har aktiverat Azure Defender ändringar i Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Vilka planer erbjuds av Security Center?](#what-are-the-plans-offered-by-security-center)
- [Hur aktiverar jag Azure Defender för min prenumeration?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Kan jag aktivera Azure Defender för en delmängd av servrarna i min prenumeration?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Om jag redan har en licens för Microsoft Defender för slutpunkt kan jag få rabatt för Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Min prenumeration har Azure Defender för servrar aktiverat, betalar jag för servrar som inte körs?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Kommer jag att debiteras för datorer utan att Log Analytics-agenten är installerad?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Om en Log Analytics-agent rapporterar till flera arbetsytor, kommer jag att debiteras två gånger?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Är den kostnadsfria datainmatningen på 500 MB tillgänglig för alla om en Log Analytics-agent rapporterar till flera arbetsytor?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Beräknas den kostnadsfria datainmatningen på 500 MB för en hel arbetsyta eller strikt per dator?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Vilka datatyper ingår i den dagliga traktamentet på 500 MB data?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Hur kan jag spåra vem i min organisation som har aktiverat Azure Defender ändringar i Security Center?
Azure-prenumerationer kan ha flera administratörer med behörighet att ändra prisinställningarna. Om du vill ta reda på vilken användare som har gjort en ändring använder du Azure-aktivitetsloggen.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Azure-aktivitetslogg som visar en händelse för prisändring":::

Om användarens information inte visas i kolumnen Event **initiated by** (Händelse initierad av) kan du utforska händelsens JSON för att få relevant information.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="JSON-utforskaren för Azure-aktivitetslogg":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Vilka planer erbjuds av Security Center? 
Security Center har två erbjudanden: 

- Kostnadsfritt Azure Security Center 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Hur aktiverar jag Azure Defender för min prenumeration? 
Du kan använda något av följande sätt för att aktivera Azure Defender för din prenumeration: 

| Metod                                          | Instruktioner                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Security Center sidor i Azure Portal | [Aktivera Azure Defender](enable-azure-defender.md)                                                                                                  |
| REST-API                                        | [Prissättnings-API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Prissättning för paket](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Kan jag aktivera Azure Defender för en delmängd av servrarna i min prenumeration?
Nej. När du [Azure Defender för servrar](defender-for-servers-introduction.md) i en prenumeration skyddas alla servrar i prenumerationen av Azure Defender. 

Ett alternativ är att aktivera Azure Defender för servrar på Log Analytics-arbetsytenivå. Om du gör detta skyddas och faktureras endast servrar som rapporterar till den arbetsytan. Flera funktioner är dock inte tillgängliga. Dessa omfattar just-in-time-åtkomst till virtuella datorer, nätverksidentifiering, regelefterlevnad, anpassningsbar nätverkshärdning, anpassningsbar programkontroll med mera. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Om jag redan har en licens för Microsoft Defender för slutpunkt kan jag få rabatt för Azure Defender?
Om du redan har en licens för Microsoft Defender for Endpoint behöver du inte betala för den delen av din Azure Defender licens.

Om du vill bekräfta rabatten kontaktar Security Center supportteamet och anger relevant arbetsyte-ID, region och licensinformation för varje relevant licens.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Min prenumeration har Azure Defender för servrar aktiverade, betalar jag för servrar som inte körs? 
Nej. När du [Azure Defender](defender-for-servers-introduction.md) för servrar i en prenumeration debiteras du inte för datorer som är i friläge medan de är i det tillståndet. Datorer debiteras enligt energisparläge enligt följande tabell:

| Stat        | Beskrivning                                                                                                                                      | Fakturerad instansanvändning |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Startar     | Den virtuella datorn startas.                                                                                                                               | Faktureras inte            |
| Körs      | Normalt arbetstillstånd för en virtuell dator                                                                                                                    | Faktureras                |
| Stoppas     | Detta är ett övergångstillstånd. När det är klart visas det som Stoppad.                                                                           | Faktureras                |
| Stoppad      | Den virtuella datorn har stängts av från gästoperativsystemet eller via PowerOff-API:erna. Maskinvaran allokeras fortfarande till den virtuella datorn och finns kvar på värden. | Faktureras                |
| Frigör | Övergångstillstånd. När den är klar visas den virtuella datorn som Frisallokerad.                                                                             | Faktureras inte            |
| Frigjord  | Den virtuella datorn har stoppats och tagits bort från värden.                                                                                  | Faktureras inte            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Azure Virtual Machines visar en frisallokerad dator":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Kommer jag att debiteras för datorer utan att Log Analytics-agenten är installerad?
Ja. När du [Azure Defender](defender-for-servers-introduction.md) för servrar i en prenumeration får datorerna i prenumerationen en mängd skydd även om du inte har installerat Log Analytics-agenten.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Om en Log Analytics-agent rapporterar till flera arbetsytor, kommer jag att debiteras två gånger? 
Ja. Om du har konfigurerat Log Analytics-agenten för att skicka data till två eller flera olika Log Analytics-arbetsytor (flera platser) debiteras du för varje arbetsyta som har lösningar för säkerhet eller program mot skadlig programvara installerade. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Är den kostnadsfria datainmatningen på 500 MB tillgänglig för alla om en Log Analytics-agent rapporterar till flera arbetsytor?
Ja. Om du har konfigurerat Log Analytics-agenten att skicka data till två eller flera olika Log Analytics-arbetsytor (flera platser) får du 500 MB kostnadsfri datainmatning. Den beräknas per nod, per rapporterad arbetsyta och per dag och är tillgänglig för alla arbetsytor som har installerade lösningar för "Säkerhet" eller "Program mot skadlig programvara". Du debiteras för alla data som matas in över 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Beräknas den kostnadsfria datainmatningen på 500 MB för en hel arbetsyta eller strikt per dator?
Du får 500 MB kostnadsfri datainmatning per dag för varje dator som är ansluten till arbetsytan. Mer specifikt för säkerhetsdatatyper som samlas in direkt av Azure Security Center.

Dessa data är ett dagligt genomsnitt för alla noder. Så även om vissa datorer skickar 100 MB och andra skickar 800 MB debiteras du inte extra om summan inte överskrider **gränsen [antal datorer] x 500 MB** ledigt.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Vilka datatyper ingår i den dagliga traktamentet för data på 500 MB?

Security Center faktureringen är nära kopplad till faktureringen för Log Analytics. Security Center en allokering på 500 MB/nod/dag mot följande delmängd [säkerhetsdatatyper:](/azure/azure-monitor/reference/tables/tables-category#security)
- WindowsEvent
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- SkadligIPCommunication
- LinuxAuditLog
- SysmonEvent
- ProtectionStatus
- Datatyperna Update och UpdateSummary när Uppdateringshantering inte körs på arbetsytan eller om mål för lösningen har aktiverats

Om arbetsytan är i den äldre prisnivån Per nod kombineras Security Center och Log Analytics-allokeringar och tillämpas gemensamt på alla fakturerbara indata.

## <a name="next-steps"></a>Nästa steg
I den här Security Center beskrivs de olika prisalternativen. För relaterat material, se:

- [Optimera dina kostnader för Azure-arbetsbelastningar](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Prisinformation i valfri valuta och i enlighet med din region](https://azure.microsoft.com/pricing/details/security-center/)
- Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter. [Med lösningsinriktning](../azure-monitor/insights/solution-targeting.md) kan du tillämpa ett omfång på lösningen och rikta in dig på en delmängd av datorerna i arbetsytan. Om du använder lösningsmål kan Security Center visar arbetsytan som att den inte har någon lösning.
