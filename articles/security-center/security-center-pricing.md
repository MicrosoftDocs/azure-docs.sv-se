---
title: Azure Security Center kostnads fri vs Azure Defender aktiverat
description: Lär dig mer om fördelarna med att aktivera Azure-skydd för skydd av arbets belastning i Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/08/2021
ms.openlocfilehash: d45dae8b0b3725555bd83a05032339671a9595be
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454372"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center kostnads fri vs Azure Defender aktiverat
Azure Defender är kostnads fritt under de första 30 dagarna. Om du väljer att fortsätta använda tjänsten i slutet av 30 dagar börjar vi automatiskt att debitera för användning.

Du kan uppgradera från sidan **priser & inställningar** , enligt beskrivningen i [snabb start: Aktivera Azure Defender](enable-azure-defender.md). Information om priser i din valuta och enligt din region finns [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Vilka är fördelarna med att aktivera Azure Defender?

Security Center erbjuds i två lägen:

- **Azure Defender off** (kostnads fri) – Security Center utan Azure Defender är aktiverat för alla dina Azure-prenumerationer när du besöker Azure Security Center instrument panelen i Azure Portal för första gången, eller om den har Aktiver ATS via programmering via API. Med det här kostnads fria läget kan du skydda dina Azure-resurser genom att använda säkerhets principer, kontinuerlig säkerhets utvärdering och åtgärds bara säkerhets rekommendationer.

- **Azure Defender on** -aktivera Azure Defender utökar funktionerna i det kostnads fria läget till arbets belastningar som körs i privata och andra offentliga moln, vilket ger enhetlig säkerhets hantering och skydd mot hot i dina hybrid moln arbets belastningar. Några av de viktigaste funktionerna i Azure Defender:

    - **Microsoft Defender för slut punkt** – Azure Defender for-servrar innehåller [Microsoft Defender för slut punkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender) för omfattande slut punkts identifiering och-svar (EDR). Lär dig mer om fördelarna med att använda Microsoft Defender för slut punkt tillsammans med Azure Defender som [används Security Center s integrerade EDR-lösning](security-center-wdatp.md).
    - **Sårbarhets sökning för virtuella datorer och behållar register** – du kan enkelt distribuera en skanner till alla virtuella datorer som tillhandahåller branschens mest avancerade lösning för sårbarhets hantering. Visa, Undersök och åtgärda resultaten direkt i Security Center. 
    - **Hybrid säkerhet** – få en enhetlig vy över säkerheten i alla dina lokala och molnbaserade arbets belastningar. Tillämpa säkerhets principer och utvärdera kontinuerligt säkerheten för dina hybrid moln arbets belastningar för att säkerställa efterlevnaden av säkerhets standarder. Samla in, Sök och analysera säkerhets data från flera källor, inklusive brand väggar och andra partner lösningar.
    - **Hot skydds aviseringar** – avancerad beteende analys och Microsoft Intelligent Security Graph ger en gräns för de växande cyberhot-angrepp. Inbyggd beteende analys och maskin inlärning kan identifiera attacker och utnyttjandet på noll dagar. Övervaka nätverk, datorer och moln tjänster för inkommande attacker och aktiviteter efter intrång. Effektivisera undersökningen med interaktiva verktyg och sammanhangsbaserad Hot information.
    - **Åtkomst-och program kontroller** (AAC) – blockera skadlig kod och andra oönskade program genom att använda rekommendationer för Machine Learning som är anpassade till dina specifika arbets belastningar för att skapa listor över tillåtna och nekade. Minska nätverks attack ytan med just-in-Time-kontrollerad åtkomst till hanterings portar på virtuella Azure-datorer. AAC minskar drastiskt exponeringen med brute force och andra nätverks attacker.
    - **Behållar säkerhetsfunktioner** – dra nytta av sårbarhets hantering och skydd mot Real tids skydd i dina behållares miljöer. När du aktiverar **Azure Defender för behållar register** kan det ta upp till 12 timmar tills alla funktioner har Aktiver ATS. Avgifterna baseras på antalet unika behållar avbildningar som push-överförts till det anslutna registret. När en bild har genomsökts en gång, kommer du inte att debiteras för den igen om den inte ändras och skickas en gång till.
    - **Bredd skydd för resurser som är anslutna till Azure-miljön** – Azure Defender innehåller Azure-inbyggt bredd skydd för de Azure-tjänster som är gemensamma för alla dina resurser: Azure Resource Manager, Azure DNS, Azure nätverks lager och Azure Key Vault. Azure Defender har unik insyn i Azures hanterings lager och Azure DNS skiktet, och kan därför skydda moln resurser som är anslutna till dessa lager.


## <a name="faq---pricing-and-billing"></a>Vanliga frågor och svar – priser och fakturering 

- [Hur kan jag spåra vem i organisationen aktiverade Azure Defender-ändringar i Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Vilka är de planer som erbjuds av Security Center?](#what-are-the-plans-offered-by-security-center)
- [Hur aktiverar jag Azure Defender för min prenumeration?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Kan jag aktivera Azure Defender för en delmängd av servrarna i min prenumeration?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Om jag redan har en licens för Microsoft Defender för slut punkten får jag en rabatt för Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Min prenumeration har Azure Defender för servrar aktiverat, betalar jag för servrar som inte är igång?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Kommer jag att debiteras för datorer utan att Log Analytics-agenten är installerad?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Kommer jag att debiteras två gånger om en Log Analytics agent rapporterar till flera arbets ytor?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Om en Log Analytics agent rapporterar till flera arbets ytor, är 500 MB kostnads fri data inmatning tillgänglig på alla?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Beräknas den 500 MB lediga data inmatningen för en hel arbets yta eller exakt per dator?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Hur kan jag spåra vem i organisationen aktiverade Azure Defender-ändringar i Security Center?
Azure-prenumerationer kan ha flera administratörer som har behörighet att ändra pris inställningarna. Om du vill ta reda på vilken användare som har gjort en ändring använder du Azure-aktivitets loggen.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Azure aktivitets loggen visar en händelse för pris ändring":::

Om användarens info inte visas i kolumnen **händelse som initieras av** , undersöker du händelsens JSON för att se om det finns mer information.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure aktivitets logg JSON-Utforskare":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Vilka är de planer som erbjuds av Security Center? 
Security Center har två erbjudanden: 

- Kostnadsfritt Azure Security Center 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Hur aktiverar jag Azure Defender för min prenumeration? 
Du kan använda något av följande sätt för att aktivera Azure Defender för din prenumeration: 

| Metod                                          | Instruktioner                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Security Center sidor i Azure Portal | [Aktivera Azure Defender](enable-azure-defender.md)                                                                                                  |
| REST-API                                        | [Prissättnings-API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [AZ Security-prissättning](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Paket priser](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Kan jag aktivera Azure Defender för en delmängd av servrarna i min prenumeration?
Nej. När du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md) i en prenumeration kommer alla servrar i prenumerationen att skyddas av Azure Defender. 

Ett alternativ är att aktivera Azure Defender för servrar på Log Analytics arbets ytans nivå. Om du gör detta kommer endast servrar som rapporterar till arbets ytan att skyddas och faktureras. Flera funktioner är dock inte tillgängliga. Dessa inkluderar just-in-Time VM-åtkomst, nätverks identifiering, regelefterlevnad, anpassad nätverks härdning, anpassningsbar program kontroll och mycket annat. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Om jag redan har en licens för Microsoft Defender för slut punkten får jag en rabatt för Azure Defender?
Om du redan har en licens för Microsoft Defender för slut punkt behöver du inte betala för den delen av din Azure Defender-licens.

Om du vill bekräfta rabatten kontaktar du Security Center support teamet och anger relevant arbetsyte-ID, region och licens information för varje relevant licens.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Min prenumeration har Azure Defender för servrar aktiverat, betalar jag för servrar som inte är igång? 
Nej. När du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md) i en prenumeration debiteras du inte för datorer som har ett friallokerat energi tillstånd när de befinner sig i det aktuella läget. Datorer faktureras enligt det energi tillstånd som visas i följande tabell:

| Stat        | Beskrivning                                                                                                                                      | Användning av instans fakturerad |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Startar     | Den virtuella datorn startas.                                                                                                                               | Ej fakturerat            |
| Körs      | Normalt arbets läge för en virtuell dator                                                                                                                    | Debiteras                |
| Stoppas     | Detta är ett över gångs tillstånd. När det är slutfört visas det som stoppad.                                                                           | Debiteras                |
| Stoppad      | Den virtuella datorn har stängts av från gäst operativ systemet eller använder avstängnings läge-API: er. Maskin varan är fortfarande allokerad till den virtuella datorn och den finns kvar på värden. | Debiteras                |
| Frigör | Över gångs tillstånd. När den är klar visas den virtuella datorn som frigjord.                                                                             | Ej fakturerat            |
| Frigjord  | Den virtuella datorn har stoppats och tagits bort från värden.                                                                                  | Ej fakturerat            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Azure Virtual Machines som visar en fribelagd dator":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Kommer jag att debiteras för datorer utan att Log Analytics-agenten är installerad?
Ja. När du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md) i en prenumeration får datorerna i den prenumerationen ett antal skydd även om du inte har installerat Log Analytics agenten.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Kommer jag att debiteras två gånger om en Log Analytics agent rapporterar till flera arbets ytor? 
Ja. Om du har konfigurerat Log Analytics-agenten för att skicka data till två eller flera olika Log Analytics arbets ytor (Multi-värdar) debiteras du för alla arbets ytor som har en säkerhets-eller Antiskadlig kod-lösning installerad. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Om en Log Analytics agent rapporterar till flera arbets ytor, är 500 MB kostnads fri data inmatning tillgänglig på alla?
Ja. Om du har konfigurerat Log Analytics-agenten för att skicka data till två eller flera olika Log Analytics arbets ytor (Multi-värdar) får du 500 MB kostnads fri data inmatning. Den beräknas per nod, per rapporterad arbets yta, per dag och tillgänglig för alla arbets ytor som har en säkerhets-eller Antiskadlig kod-lösning installerad. Du debiteras för alla data som matas in över 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Beräknas den 500 MB lediga data inmatningen för en hel arbets yta eller exakt per dator?
Du får 500 MB kostnads fri data inmatning per dag för varje dator som är ansluten till arbets ytan. Specifikt för säkerhets data typer som samlas in direkt av Azure Security Center.

Dessa data är ett dagligt genomsnitt för alla noder. Så även om vissa datorer skickar 100-MB och andra skickar 800-MB, om summan inte överstiger **[antal datorer] x 500-MB** fri gräns, debiteras du inte extra.

## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras Security Center pris alternativ. För relaterat material, se:

- [Optimera dina Azure-arbetsbelastnings kostnader](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Pris information i valfri valuta och enligt din region](https://azure.microsoft.com/pricing/details/security-center/)
- Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter. Med [lösnings mål](../azure-monitor/insights/solution-targeting.md) kan du tillämpa en omfattning för lösningen och rikta in dig på en delmängd av datorerna i arbets ytan. Om du använder lösnings mål, Security Center visar arbets ytan som en lösning.