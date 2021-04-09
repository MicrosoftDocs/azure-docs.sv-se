---
title: Snabb start – distribuera och hantera NSG Flow-loggar med Azure Policy
titleSuffix: Azure Network Watcher
description: Den här artikeln förklarar hur du använder de inbyggda principerna för att hantera distributionen av NSG Flow-loggar
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8da1130809c1802f4db963f4b4b000a848e9abaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011109"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Snabb start: Distribuera och hantera NSG Flow-loggar med Azure Policy 

## <a name="overview"></a>Översikt
Azure Policy hjälper till att framtvinga organisationsstandarder och utvärdera efterlevnad i stor skala. Vanliga användnings fall för Azure Policy inkluderar implementering av styrning för resurs konsekvens, regelefterlevnad, säkerhet, kostnad och hantering. I den här artikeln kommer vi att använda två inbyggda principer som är tillgängliga för NSG flödes loggar för att hantera konfigurationen av Flow-loggar. Den första principen flaggar alla NSG: er utan flödes loggar aktiverade. Den andra principen distribuerar automatiskt flödes loggar för NSG: er utan att flödes loggar Aktiver ATS. 

Om du skapar en Azure-princip för första gången kan du läsa följande: 
- [Översikt över Azure Policy](../governance/policy/overview.md) 
- [Självstudie för att skapa en princip](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Hitta principerna
1. Gå till Azure Portal – [Portal.Azure.com](https://portal.azure.com) 

Gå till Azure Policy sida genom att söka efter princip i den övre start sidan för search bar- ![ principer](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Gå till fliken **tilldelningar** i det vänstra fönstret

![Fliken tilldelningar](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Klicka på knappen **tilldela princip** 

![Knappen tilldela princip](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Klicka på menyn tre punkter under "princip definitioner" om du vill se tillgängliga principer

5. Använd typ filtret och välj "inbyggd". Sök sedan efter "flödes logg"

Du bör se de två inbyggda principerna för flödes loggar ![ princip lista](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Välj den princip som du vill tilldela

- *"Flödes loggen ska konfigureras för varje nätverks säkerhets grupp"* är en gransknings princip som flaggar icke-kompatibel NSG: er, som är NSG: er utan flödes loggning aktive rad
- *"Distribuera en flödes logg resurs med mål nätverks säkerhets gruppen"* är principen med en distributions åtgärd, den aktiverar flödes loggar på alla NSG: er utan flödes loggar

Det finns separata instruktioner för varje princip nedan.  

## <a name="audit-policy"></a>Granskningsprincip 

### <a name="how-the-policy-works"></a>Så här fungerar principen

Principen kontrollerar alla befintliga ARM-objekt av typen "Microsoft. Network/networkSecurityGroups", som visas på alla NSG: er i ett angivet omfång och kontrollerar om det finns länkade flödes loggar via egenskapen Flow-loggar för NSG. Om egenskapen inte finns flaggas NSG.

Om du vill se en fullständig definition av principen kan du gå till [fliken definitioner](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) och söka efter "Flow-loggar" för att hitta principen

### <a name="assignment"></a>Tilldelning

1. Fyll i princip informationen

- Omfattning: en prenumeration är det vanligaste valet. du kan också välja en hanterings grupp eller resurs grupp som är relevant för dig.  
- Princip definition: ska väljas enligt beskrivningen i avsnittet "hitta principer".
- AssignmentName: Välj ett beskrivande namn 

2. Klicka på "granska + skapa" för att granska tilldelningen

Principen kräver inte några parametrar. När du tilldelar en gransknings princip behöver du inte fylla i informationen på fliken "reparation".  

![Gransknings princip granskning](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Resultat

Du kontrollerar resultaten genom att öppna fliken efterlevnad och söka efter namnet på din tilldelning.
Du bör se något som liknar följande skärm bild när principen körs. Om principen inte har körts väntar du en stund. 

![Gransknings princip resultat](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Distribuera-IF-not-exists-princip 

### <a name="policy-structure"></a>Princip struktur

Principen kontrollerar alla befintliga ARM-objekt av typen "Microsoft. Network/networkSecurityGroups", som visas på alla NSG: er i ett angivet omfång och kontrollerar om det finns länkade flödes loggar via egenskapen Flow-loggar för NSG. Om egenskapen inte finns distribuerar principen en flödes logg. 

Om du vill se en fullständig definition av principen kan du gå till [fliken definitioner](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) och söka efter "Flow-loggar" för att hitta principen. 

### <a name="assignment"></a>Tilldelning

1. Fyll i princip informationen

- Omfattning: en prenumeration är det vanligaste valet. du kan också välja en hanterings grupp eller resurs grupp som är relevant för dig.  
- Princip definition: ska väljas enligt beskrivningen i avsnittet "hitta principer".
- AssignmentName: Välj ett beskrivande namn 

2. Lägg till princip parametrar 

Tjänsten Network Watcher är en regional tjänst. Dessa parametrar gör att princip åtgärden för att distribuera flödes loggar ska köras. 
- NSG region: Azure-regioner där principen är riktad
- Lagrings-ID: lagrings kontots fullständiga resurs-ID. Obs! det här lagrings kontot ska finnas i samma region som NSG. 
- Network Watcher RG: namnet på den resurs grupp som innehåller din Network Watcher-resurs. Om du inte har bytt namn på den kan du ange "NetworkWatcherRG" som är standard.
- Network Watcher namn: namnet på den regionala Network Watcher-tjänsten. Format: NetworkWatcher_RegionName. Exempel: NetworkWatcher_centralus. Se hela listan.

![DINE princip parametrar](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Lägg till reparations information

- Markera "skapa reparations uppgift" om du vill att principen ska påverka befintliga resurser 
- "Skapa en hanterad identitet" ska redan vara markerad
- Har valt samma plats som tidigare för din hanterade identitet 
- Du måste ha deltagar-eller ägar behörigheter för att använda den här principen. Om du har dessa behörigheter bör du inte se några fel.

![Reparation av DINE-principer](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Klicka på "granska + skapa" för att granska din tilldelning. du bör se något som liknar följande skärm bild.

![DINE princip granskning](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Resultat

Du kontrollerar resultaten genom att öppna fliken efterlevnad och söka efter namnet på din tilldelning.
Du bör se något som liknar följande skärm bild när principen. Om principen inte har körts väntar du en stund.

![DINE princip resultat](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Nästa steg 

-   Använd den här [självstudien](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) gå djupare genom att använda arm-mallar för att distribuera flödes loggar och trafikanalys.
-   Läs mer om [Network Watcher](./index.yml)