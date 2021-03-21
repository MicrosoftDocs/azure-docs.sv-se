---
title: Automatisk skalning v1
description: Automatisk skalning och App Service-miljön v1. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021664"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatisk skalning och App Service-miljön v1

> [!NOTE]
> Den här artikeln gäller App Service-miljön v1.  Det finns en nyare version av App Service-miljön som är enklare att använda och som körs på en kraftfullare infrastruktur. Om du vill veta mer om den nya versionen börjar [du med introduktionen till App Service-miljön](intro.md).
> 

Azure App Service miljöer stöder *autoskalning*. Du kan skala enskilda Worker-pooler automatiskt baserat på mått eller schema.

![Alternativ för autoskalning för en arbets grupp.][intro]

Automatisk skalning optimerar resursanvändningen genom att automatiskt öka och minska en App Services miljö så att den passar din budget och eller läser in profil.

## <a name="configure-worker-pool-autoscale"></a>Konfigurera autoskalning för arbets pool
Du kan komma åt funktionen för autoskalning från fliken **Inställningar** i arbets gruppen.

![Fliken Inställningar i arbets gruppen.][settings-scale]

Därifrån bör gränssnittet vara ganska välbekant eftersom det är samma upplevelse som du ser när du skalar en App Service plan. 

![Inställningar för manuell skalning.][scale-manual]

Du kan också konfigurera en profil för autoskalning.

![Inställningar för autoskalning.][scale-profile]

Profiler för autoskalning är användbara om du vill ange gränser för skalan. På så sätt kan du ha en konsekvent prestanda upplevelse genom att ange ett lägre gräns värde (1) och en förutsägbar kostnad genom att ange en övre gräns (2).

![Skalnings inställningar i profilen.][scale-profile2]

När du har definierat en profil kan du lägga till regler för autoskalning för att skala upp eller ned antalet instanser i arbets gruppen inom de gränser som definieras av profilen. Regler för autoskalning baseras på mått.

![Skalnings regel.][scale-rule]

 Alla Worker-pooler och frontend-mått kan användas för att definiera regler för autoskalning. Dessa mått är samma mått som du kan övervaka i bladet resurs blad eller ange aviseringar för.

## <a name="autoscale-example"></a>Exempel på autoskalning
Autoskalning av en App Service miljö kan visas bäst genom att du går igenom ett scenario.

I den här artikeln beskrivs alla nödvändiga överväganden när du ställer in autoskalning. Artikeln vägleder dig genom de interaktioner som kommer i spel när du använder den automatiska skalnings App Service miljöer som finns i App Service-miljön.

### <a name="scenario-introduction"></a>Introduktion till scenario
Frank är en sysadmin för ett företag som har migrerat en del av arbets belastningarna som de hanterar till en App Services miljö.

App Services miljön har kon figurer ATS för manuell skalning enligt följande:

* **Klient delar:** 3
* **Arbets pool 1**: 10
* **Arbets pool 2**: 5
* **Arbets pool 3**: 5

Worker-pool 1 används för produktions arbets belastningar, medan Worker pool 2 och Worker pool 3 används för kvalitets säkrings-och utvecklings arbets belastningar.

App Service planer för frågor och utveckling har kon figurer ATS för manuell skalning. Produktions App Service plan är inställd på automatisk skalning för att hantera variationer i belastning och trafik.

Frank är mycket bekant med programmet. De vet att belastningen för belastningen är mellan 9:00 och 6:00 PM eftersom det är ett LOB-program (Line-of-Business) som medarbetarna använder när de är på kontoret. Användningen sjunker efter det när användarna är klara för den dagen. Vid låg belastnings tider finns det fortfarande en del belastning eftersom användarna kan komma åt appen via en fjärr anslutning med hjälp av sina mobila enheter eller hem datorer. Produktions App Service plan har redan kon figurer ATS för autoskalning baserat på CPU-användning med följande regler:

![Vissa inställningar för LOB-appen.][asp-scale]

| **Profil för autoskalning – vardagar – App Service plan** | **Profil för autoskalning – helg – App Service plan** |
| --- | --- |
| **Namn:** Veckodag-profil |**Namn:** Helg profil |
| **Skala efter:** Schema-och prestanda regler |**Skala efter:** Schema-och prestanda regler |
| **Profil:** Vecko dagar |**Profil:** Helgen |
| **Typ:** Mönster |**Typ:** Mönster |
| **Mål intervall:** 5 till 20 instanser |**Mål intervall:** 3 till 10 instanser |
| **Dagar:** Måndag, tisdag, onsdag, torsdag, fredag |**Dagar:** Lördag, söndag |
| **Start tid:** 9:00 am |**Start tid:** 9:00 am |
| Tidszon **:** UTC-08 |Tidszon **:** UTC-08 |
|  | |
| **Regel för autoskalning (skala upp)** |**Regel för autoskalning (skala upp)** |
| **Resurs:** Produktion (App Service-miljön) |**Resurs:** Produktion (App Service-miljön) |
| **Mått:** REGISTRERA |**Mått:** REGISTRERA |
| **Åtgärd:** Större än 60% |**Åtgärd:** Större än 80% |
| **Varaktighet:** 5 minuter |**Varaktighet:** 10 minuter |
| **Tids mängd:** Snitt |**Tids mängd:** Snitt |
| **Åtgärd:** Öka antal med 2 |**Åtgärd:** Öka antal med 1 |
| Låg frekvent **(minuter):** 15 |Låg frekvent **(minuter):** 20 |
|  | |
| **Regel för autoskalning (skala ned)** |**Regel för autoskalning (skala ned)** |
| **Resurs:** Produktion (App Service-miljön) |**Resurs:** Produktion (App Service-miljön) |
| **Mått:** REGISTRERA |**Mått:** REGISTRERA |
| **Åtgärd:** Mindre än 30% |**Åtgärd:** Mindre än 20% |
| **Varaktighet:** 10 minuter |**Varaktighet:** 15 minuter |
| **Tids mängd:** Snitt |**Tids mängd:** Snitt |
| **Åtgärd:** Minska antal med 1 |**Åtgärd:** Minska antal med 1 |
| Låg frekvent **(minuter):** 20 |Låg frekvent **(minuter):** 10 |

### <a name="app-service-plan-inflation-rate"></a>App Service plan inflations takt
App Service planer som kon figurer ATS för autoskalning så till en högsta pris per timme. Den här hastigheten kan beräknas baserat på de värden som anges i regeln för autoskalning.

Att förstå och beräkna den *App Service plan inflations takten* är viktigt för att App Service miljön Autoskala eftersom skalnings ändringar till en arbets grupp inte är omedelbara.

App Service plan inflations takten beräknas på följande sätt:

![Beräkning av App Service plan inflations grad.][ASP-Inflation]

Baserat på regeln för autoskalning – skala upp för vardags profilen för produktions App Service plan:

![App Service plan inflations takt för vardagar baserat på regler för autoskalning – skala upp.][Equation1]

När det gäller autoskalning – skala upp-regeln för den här produktions App Service planens helg profil, matcha formeln till:

![App Service plan inflations takt för helger baserat på regler för autoskalning – skala upp.][Equation2]

Det här värdet kan också beräknas för skalnings åtgärder.

Baserat på den automatiska skalnings regeln för den vardags profil som gäller för produktions App Service plan, ser detta ut så här:

![App Service plan inflations takt för vardagar baserat på regler för autoskalning – skala nedåt.][Equation3]

När det gäller den automatiska skalnings regeln för den här produktions App Service planens helg profil, matcha formeln till:  

![App Service plan inflations takt för helger baserat på regler för autoskalning – skala nedåt.][Equation4]

Produktions App Service plan kan växa med högst åtta instanser/timme under veckan och fyra instanser/timme under helgen. Den kan släppa instanser på högst fyra instanser/timme under veckan och sex instanser/timme under helger.

Om flera App Service-planer finns i en arbets grupp måste du beräkna den *totala inflations taxan* som summan av inflations priset för alla App Service-planer som är värdar för den aktuella arbets gruppen.

![Den totala inflations beräkningen för flera App Service planer som finns i en arbets grupp.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Använd App Service plan inflations takt för att definiera regler för autoskalning av arbets pool
Resurspooler som är värdar för App Service planer som är konfigurerade för autoskalning måste allokeras till en buffert med kapacitet. Bufferten gör att de automatiska skalnings åtgärderna kan växa och minska App Service plan efter behov. Den lägsta bufferten skulle vara den beräknade totala App Services planens inflations takt.

Eftersom App Service miljö skalnings åtgärder tar lite tid att tillämpa, bör alla ändringar redovisas för ytterligare efter frågan som kan uppstå medan en skalnings åtgärd pågår. För att tillgodose den här fördröjningen rekommenderar vi att du använder den beräknade totala App Services planens inflations takt som minsta antal instanser som läggs till för varje autoskalning-åtgärd.

Med den här informationen kan Frank definiera följande profil och regler för autoskalning:

![Profil regler för autoskalning för LOB-exempel.][Worker-Pool-Scale]

| **Profil för autoskalning – vardagar** | **Skala profil – helger** |
| --- | --- |
| **Namn:** Veckodag-profil |**Namn:** Helg profil |
| **Skala efter:** Schema-och prestanda regler |**Skala efter:** Schema-och prestanda regler |
| **Profil:** Vecko dagar |**Profil:** Helgen |
| **Typ:** Mönster |**Typ:** Mönster |
| **Mål intervall:** 13 till 25 instanser |**Mål intervall:** 6 till 15 instanser |
| **Dagar:** Måndag, tisdag, onsdag, torsdag, fredag |**Dagar:** Lördag, söndag |
| **Start tid:** 7:00 am |**Start tid:** 9:00 am |
| Tidszon **:** UTC-08 |Tidszon **:** UTC-08 |
|  | |
| **Regel för autoskalning (skala upp)** |**Regel för autoskalning (skala upp)** |
| **Resurs:** Arbets pool 1 |**Resurs:** Arbets pool 1 |
| **Mått:** WorkersAvailable |**Mått:** WorkersAvailable |
| **Åtgärd:** Mindre än 8 |**Åtgärd:** Mindre än 3 |
| **Varaktighet:** 20 minuter |**Varaktighet:** 30 minuter |
| **Tids mängd:** Snitt |**Tids mängd:** Snitt |
| **Åtgärd:** Öka antal med 8 |**Åtgärd:** Öka antalet med 3 |
| Låg frekvent **(minuter):** 180 |Låg frekvent **(minuter):** 180 |
|  | |
| **Regel för autoskalning (skala ned)** |**Regel för autoskalning (skala ned)** |
| **Resurs:** Arbets pool 1 |**Resurs:** Arbets pool 1 |
| **Mått:** WorkersAvailable |**Mått:** WorkersAvailable |
| **Åtgärd:** Större än 8 |**Åtgärd:** Större än 3 |
| **Varaktighet:** 20 minuter |**Varaktighet:** 15 minuter |
| **Tids mängd:** Snitt |**Tids mängd:** Snitt |
| **Åtgärd:** Minska antal med 2 |**Åtgärd:** Minska antalet med 3 |
| Låg frekvent **(minuter):** 120 |Låg frekvent **(minuter):** 120 |

Det angivna mål intervallet i profilen beräknas av de minsta instanserna som definierats i profilen för App Service plan + buffer.

Det maximala intervallet är summan av alla maximala intervall för alla App Service planer som finns i arbets gruppen.

Öknings antalet för reglerna för skalnings regler ska vara minst 1X App Service plans inflations takt för skalning.

Antalet minskningar kan justeras till något mellan 1/2X eller 1X App Service plan inflations takt för skalning.

### <a name="autoscale-for-front-end-pool"></a>Autoskalning för klient delens pool
Regler för autoskalning på klient sidan är enklare än för Worker-pooler. Främst bör du  
Se till att varaktigheten för mätningen och cooldown-förstenarna anser att skalnings åtgärder på en app service plan inte är omedelbara.

I det här scenariot vet Frank att fel frekvensen ökar efter att klient lanseringarna når 80% processor användning och ställer in regeln för autoskalning för att öka instanser på följande sätt:

![Inställningar för autoskalning för klient delens pool.][Front-End-Scale]

| **Profil för autoskalning – klient delar** |
| --- |
| **Namn:** Autoskalning – klient delar |
| **Skala efter:** Schema-och prestanda regler |
| **Profil:** Vardag |
| **Typ:** Mönster |
| **Mål intervall:** 3 till 10 instanser |
| **Dagar:** Vardag |
| **Start tid:** 9:00 am |
| Tidszon **:** UTC-08 |
|  |
| **Regel för autoskalning (skala upp)** |
| **Resurs:** Klient delens pool |
| **Mått:** REGISTRERA |
| **Åtgärd:** Större än 60% |
| **Varaktighet:** 20 minuter |
| **Tids mängd:** Snitt |
| **Åtgärd:** Öka antalet med 3 |
| Låg frekvent **(minuter):** 120 |
|  |
| **Regel för autoskalning (skala ned)** |
| **Resurs:** Arbets pool 1 |
| **Mått:** REGISTRERA |
| **Åtgärd:** Mindre än 30% |
| **Varaktighet:** 20 minuter |
| **Tids mängd:** Snitt |
| **Åtgärd:** Minska antalet med 3 |
| Låg frekvent **(minuter):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
