---
title: Azure Functions Premium-plan
description: Information och konfigurations alternativ (VNet, ingen kall start, obegränsad körnings tid) för Azure Functions Premium-planen.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: 3061329ad9dcb368dab586acc2146e6fb4e23028
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708721"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions Premium-plan

Azure Functions Premium-planen (kallas ibland elastisk Premium-plan) är ett värd alternativ för Function-appar. För andra alternativ för värd abonnemang, se [artikeln värd plan](functions-scale.md).

Premium plan-värd ger följande fördelar för dina funktioner:

* Undvik kall börjar med ständigt varma instanser
* Anslutning till virtuellt nätverk.
* Obegränsad körnings tid, med 60 minuter garanterad.
* Premium-instans storlekar: en kärna, två kärnor och fyra core-instanser.
* Mer förutsägbar prissättning jämfört med förbruknings planen.
* App-allokering med hög densitet för planer med flera Function-appar.

När du använder Premium-planen läggs instanser av Azure Functions-värden till och tas bort baserat på antalet inkommande händelser, precis som [förbruknings planen](consumption-plan.md). Flera Function-appar kan distribueras till samma Premium plan och med planen kan du konfigurera storlek på beräknings instanser, bas Plans storlek och maximal schema storlek. 

## <a name="billing"></a>Fakturering

Faktureringen för Premium-planen baseras på antalet kärn sekunder och minne som allokerats mellan instanser. Den här faktureringen skiljer sig från förbruknings planen, som faktureras per körning och använt minne. Det finns ingen körnings avgift med Premium planen. Minst en instans måste tilldelas hela tiden per plan. Den här faktureringen resulterar i en lägsta månads kostnad per aktiv prenumeration, oavsett om funktionen är aktiv eller inaktiv. Tänk på att alla funktions program i en Premium plan delar allokerade instanser. Mer information finns på sidan med [Azure Functions priser](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-premium-plan"></a>Skapa en Premium-plan

När du skapar en Function-app i Azure Portal är förbruknings planen standard. Om du vill skapa en Function-app som körs i en Premium-plan måste du explicit skapa en App Service plan med hjälp av en av de _elastiska Premium_ -SKU: erna. Den Function-app som du skapar finns sedan i den här planen. Azure Portal är det enkelt att skapa både Premium-och funktions programmet samtidigt. Du kan köra mer än en Function-app i samma Premium-plan, men de körs båda på samma operativ system (Windows eller Linux). 

I följande artiklar visas hur du skapar en Function-app med en Premium-plan, antingen via programmering eller i Azure Portal:

+ [Azure-portalen](create-premium-plan-function-app-portal.md)
+ [Azure CLI](scripts/functions-cli-create-premium-plan.md)
+ [Azure Resource Manager-mall](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>Eliminera kall starter

När händelser eller körningar inte inträffar i förbruknings planen kan din app skalas till noll instanser. När nya händelser kommer in måste en ny instans med din app som körs på den vara specialiserad. Det kan ta lite tid att utföra särskilda nya instanser beroende på appen. Den ytterligare svars tiden för det första anropet kallas ofta app _kall start_.

Premium-planen innehåller två funktioner som samarbetar för att effektivt eliminera kall start i dina funktioner: _alltid färdiga instanser_ och _förvärmade instanser_. 

### <a name="always-ready-instances"></a>Alltid färdiga instanser

I Premium-planen kan du låta appen alltid vara klar på ett angivet antal instanser. Det maximala antalet alltid färdiga instanser är 20. När händelser börjar utlösa appen dirigeras de först till de alltid färdiga instanserna. När funktionen blir aktiv kommer ytterligare instanser att värmas som en buffert. Den här bufferten förhindrar kall start för nya instanser som krävs under skalning. Dessa buffrade instanser kallas [för förvärmade instanser](#pre-warmed-instances). Med kombinationen av alltid färdiga instanser och en förvärmad buffert kan din app effektivt eliminera kall start.

> [!NOTE]
> Varje Premium-plan har alltid minst en aktiv (fakturerad) instans.

# <a name="portal"></a>[Portal](#tab/portal)

Du kan konfigurera antalet alltid färdiga instanser i Azure Portal genom att välja **Funktionsapp**, gå till fliken **plattforms funktioner** och välja alternativen för **skala ut** . I redigerings fönstret för Function-appen är alltid färdiga instanser speciella för den appen.

![Inställningar för elastisk skalning](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Du kan också konfigurera alltid färdiga instanser för en app med Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>Förvärmade instanser

Förvärmade instanser är instanser som värmas som en buffert vid skalnings-och aktiverings händelser. Förvärmade instanser fortsätter att buffra tills den maximala skalnings gränsen har uppnåtts. Standard antalet förvärmade instanser är 1 och för de flesta scenarier ska det här värdet vara 1.

När en app har en lång uppvärmning (som en anpassad behållar avbildning) kan du behöva öka den här bufferten. En förvärmad instans blir bara aktiv när alla aktiva instanser har använts.

Tänk på det här exemplet på hur alltid färdiga instanser och förvärmade instanser fungerar tillsammans. En Premium Function-app har fem alltid färdiga instanser konfigurerade och standardvärdet för en förvärmad instans. När appen är inaktiv och inga händelser utlöses, är appen etablerad och körs med fem instanser. För tillfället debiteras du inte för en förvärmad instans eftersom de alltid är redo-versioner som inte används och ingen förvärmd instans allokeras.

Så snart den första utlösaren kommer in blir de fem alltid färdiga instanserna aktiva och en förvärmad instans allokeras. Appen körs nu med sex etablerade instanser: de fem nu aktiva, alltid aktiva instanserna och den sjätte förvärmade och inaktiva bufferten. Om körnings frekvensen fortsätter att öka används de fem aktiva instanserna slutligen. När plattformen bestämmer sig för att skala bortom fem instanser, skalas den till den förvärmade instansen. När detta inträffar finns nu sex aktiva instanser, och en sjunde instans etableras genast och fyller den förvärmade bufferten. Den här sekvensen av skalning och för uppvärmning fortsätter tills det maximala antalet instanser för appen har nåtts. Inga instanser är förvärmade eller aktiverade utöver det högsta antalet.

Du kan ändra antalet förvärmade instanser för en app med hjälp av Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>Maximal funktions program instans

Förutom det [högsta antalet instanser av planen](#plan-and-sku-settings)kan du konfigurera ett maximum per app. Appens Max värde kan konfigureras med [program skalnings gränsen](./event-driven-scaling.md#limit-scale-out).

## <a name="private-network-connectivity"></a>Anslutning till privat nätverk

Function-appar som distribueras till en Premium-plan kan dra nytta av [VNet-integrering för webbappar](../app-service/web-sites-integrate-with-vnet.md). När appen har kon figurer ATS kan den kommunicera med resurser i ditt VNet eller skyddas via tjänst slut punkter. IP-begränsningar är också tillgängliga i appen för att begränsa inkommande trafik.

När du tilldelar ett undernät till din Function-app i en Premium-plan behöver du ett undernät med tillräckligt med IP-adresser för varje möjlig instans. Vi kräver ett IP-block med minst 100 tillgängliga adresser.

Mer information finns i [integrera din Function-app med ett VNet](functions-create-vnet.md).

## <a name="rapid-elastic-scale"></a>Snabb elastisk skalning

Ytterligare beräknings instanser läggs automatiskt till för din app med samma snabbt skalnings logik som förbruknings planen. Appar i samma App Services plan skalas oberoende av varandra baserat på en enskild Apps behov. Men Functions-appar i samma App Service plan delar VM-resurser för att minska kostnaderna, om möjligt. Antalet appar som är associerade med en virtuell dator beror på varje apps storlek och storleken på den virtuella datorn.

Mer information om hur skalning fungerar finns [i händelse driven skalning i Azure Functions](event-driven-scaling.md).

## <a name="longer-run-duration"></a>Varaktighet för längre körning

Azure Functions i en förbruknings plan är begränsad till 10 minuter för en enda körning. I Premium-planen är varaktigheten för körningen standardvärdet 30 minuter för att förhindra överkörningar. Du kan dock [ändra host.jsi konfigurationen](./functions-host-json.md#functiontimeout) för att göra varaktigheten obegränsad för appar för Premium-plan. När den är inställd på en obunden varaktighet, garanteras att din funktions App körs i minst 60 minuter. 

## <a name="plan-and-sku-settings"></a>Planera och SKU-inställningar

När du skapar planen finns det två inställningar för schema storlek: det minsta antalet instanser (eller plan storlek) och den maximala burst-gränsen.

Om din app kräver instanser utanför de ständigt färdiga instanserna, kan den fortsätta att skala ut tills antalet instanser träffar den maximala burst-gränsen. Du debiteras för instanser som ligger utanför din plan storlek bara när de körs och tilldelas dig, per sekund. Plattformen gör det bäst att skala ut appen till den definierade Max gränsen.

Du kan konfigurera plan storlek och Max belopp i Azure Portal genom att välja alternativen för **skala ut** i planen eller en Function-app som distribueras till den planen (under **plattforms funktioner**).

Du kan också öka den maximala burst-gränsen från Azure CLI:

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

Minimi kravet för varje plan är minst en instans. Det faktiska minsta antalet instanser konfigureras automatiskt för dig baserat på de alltid färdiga instanser som begärs av appar i planen. Om till exempel en app begär fem alltid färdiga instanser och app B begär två alltid färdiga instanser i samma plan, beräknas den minsta schema storleken som fem. App A körs på alla 5, och app B körs bara på 2.

> [!IMPORTANT]
> Du debiteras för varje instans som allokeras i minsta instans antal oavsett om funktionerna körs eller inte.

I de flesta fall räcker det här automatiskt med att beräkna. Att skala bortom det lägsta antalet inträffar dock på bästa möjliga sätt. Det är möjligt, men troligen, att vid en bestämd tids skala kan fördröjas om ytterligare instanser inte är tillgängliga. Genom att ställa in ett minimum som är högre än det autoberäknade minimivärdet reserverar du instanser i förväg av utskalning.

Att öka det beräknade minimivärdet för en plan kan göras med hjälp av Azure CLI.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>Tillgängliga instanser SKU: er

När du skapar eller skalar planen kan du välja mellan tre instans storlekar. Du debiteras för det totala antalet kärnor och minne som har allokerats, per sekund som varje instans allokeras till dig. Din app kan automatiskt skala ut till flera instanser efter behov.

|SKU|Kärnor|Minne|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|GB|
|EP2|2|7GB|GB|
|EP3|4|14 GB|GB|

### <a name="memory-usage-considerations"></a>Överväganden för minnes användning

Att köra på en dator med mer minne innebär inte alltid att din Function-app använder allt tillgängligt minne.

Till exempel begränsas en JavaScript Function-app av standard minnes gränsen i Node.js. Om du vill öka den här fasta minnes gränsen lägger du till inställningen för appen `languageWorkers:node:arguments` med värdet `--max-old-space-size=<max memory in MB>` .

För planer med mer än 4 GB minne ser du till att inställningen för bitness-plattformen är inställd på `64 Bit` under [allmänna inställningar](../app-service/configure-common.md#configure-general-settings).

## <a name="region-max-scale-out"></a>Region, Max skala ut

Nedan visas de maximala skalnings värden som stöds för närvarande för en enskild plan i varje region och OS-konfiguration. Om du vill begära en ökning kan du öppna ett support ärende.

Se den fullständiga regionala tillgängligheten för funktioner på [Azure-webbplatsen](https://azure.microsoft.com/global-infrastructure/services/?products=functions).

|Region| Windows | Linux |
|--| -- | -- |
|Australien, centrala| 100 | Inte tillgängligt |
|Australien, centrala 2| 100 | Inte tillgängligt |
|Australien, östra| 100 | 20 |
|Australien, sydöstra | 100 | 20 |
|Brasilien, södra| 100 | 20 |
|Kanada, centrala| 100 | 20 |
|Central US| 100 | 20 |
|Kina, östra 2| 100 | 20 |
|Kina, norra 2| 100 | 20 |
|Asien, östra| 100 | 20 |
|East US | 100 | 20 |
|USA, östra 2| 100 | 20 |
|Frankrike, centrala| 100 | 20 |
|Tyskland, västra centrala| 100 | Inte tillgängligt |
|Japan, östra| 100 | 20 |
|Japan, västra| 100 | 20 |
|Sydkorea, centrala| 100 | 20 |
|Sydkorea, södra| Inte tillgängligt | 20 |
|USA, norra centrala| 100 | 20 |
|Europa, norra| 100 | 20 |
|Östra Norge| 100 | 20 |
|USA, södra centrala| 100 | 20 |
|Indien, södra | 100 | Inte tillgängligt |
|Sydostasien| 100 | 20 |
|Schweiz, norra| 100 | Inte tillgängligt |
|Schweiz, västra| 100 | Inte tillgängligt |
|Storbritannien, södra| 100 | 20 |
|Storbritannien, västra| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginia| 100 | 20 |
|USNat, öst| 100 | Inte tillgängligt |
|USNat väst| 100 | Inte tillgängligt |
|Europa, västra| 100 | 20 |
|Indien, västra| 100 | 20 |
|USA, västra centrala| 100 | 20 |
|USA, västra| 100 | 20 |
|USA, västra 2| 100 | 20 |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förstå Azure Functions värd alternativ](functions-scale.md)