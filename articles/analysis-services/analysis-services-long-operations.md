---
title: Metod tips för långvariga drift åtgärder i Azure Analysis Services | Microsoft Docs
description: Den här artikeln beskriver metod tips för långvariga drift åtgärder.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92014842"
---
# <a name="best-practices-for-long-running-operations"></a>Metodtips för långvariga åtgärder

I Azure Analysis Services representerar en *nod* en virtuell värd dator där en server resurs körs. Vissa åtgärder, t. ex. tids krävande frågor, uppdaterings åtgärder och synkronisering av en fråga kan inte utföras om en server resurs flyttas till en annan nod. Vanliga fel meddelanden i det här scenariot är:

- "Ett fel uppstod vid försök att hitta en tids krävande XMLA-begäran. Begäran kan ha avbrutits av tjänst uppgradering eller omstart av servern. "
- "Jobbet med ID <guid> för modellen" <database> "avbröts på grund av ett fel i tjänsten (inaktivitet) när meddelandet avbröt uppdateringsbegäran sedan den fastnade utan några uppdateringar. Detta är ett internt tjänst problem. Skicka jobbet eller filen en biljett igen för att få hjälp om det här problemet inträffar upprepade gånger. "

Det finns många orsaker till varför tids krävande åtgärder kan avbrytas. Till exempel uppdateringar i Azure, till exempel: 
- Korrigeringsfiler för operativ system 
- Säkerhetsuppdateringar
- Azure Analysis Services tjänst uppdateringar
- Service Fabric uppdateringar. Service Fabric är en plattforms komponent som används av ett antal Microsofts moln tjänster, inklusive Azure Analysis Services.

Förutom uppdateringar som inträffar i tjänsten, finns det en naturlig flyttning av tjänster mellan noder på grund av belastnings utjämning. Nod transporter är en förväntad del av en moln tjänst. Azure Analysis Services försöker minimera påverkan från nodens rörelser, men det är omöjligt att helt ta bort dem. 

Förutom nod transporter finns det andra fel som kan uppstå. Till exempel kan ett data käll databas system vara offline eller nätverks anslutningen förloras. Om det under uppdateringen har 10 000 000 rader och ett fel uppstår på raden 9 millionth finns det inget sätt att starta om uppdateringen vid fel punkten. Tjänsten måste börja om från början. 

## <a name="refresh-rest-api"></a>Uppdatera REST API

Tjänst avbrott kan vara utmanande för tids krävande åtgärder som data uppdatering. Azure Analysis Services innehåller ett REST API som minskar negativ påverkan från tjänst avbrott. Läs mer i [asynkron uppdatering med REST API](analysis-services-async-refresh.md).
 
Förutom REST API finns det andra metoder som du kan använda för att minimera eventuella problem under tids krävande uppdaterings åtgärder. Huvud målet är att undvika att behöva starta om uppdaterings åtgärden från början och i stället utföra uppdateringar i mindre batchar som kan allokeras i steg. 
 
REST API tillåter sådan omstart, men den tillåter inte fullständig flexibilitet när det gäller att skapa och ta bort partitioner. Om ett scenario kräver komplexa data hanterings åtgärder bör din lösning innehålla någon form av satsvis kompilering i sin logik. Genom att använda transaktioner för att bearbeta data i flera kan separata batchar till exempel inte kräva omstart från början, utan i stället från en mellanliggande kontroll punkt. 
 
## <a name="scale-out-query-replicas"></a>Skala ut fråga-repliker

Oavsett om du använder REST eller anpassad logik kan klient program frågor fortfarande returnera inkonsekventa eller mellanliggande resultat medan batchar bearbetas. Om konsekventa data som returneras av klient program frågor krävs medan bearbetningen sker, och modell data är i ett mellanliggande tillstånd, kan du använda [skalbarhet](analysis-services-scale-out.md) med skrivskyddade fråga-repliker.

Genom att använda skrivskyddade repliker, medan uppdatering utförs i batchar, kan klient program användare fortsätta att fråga den gamla ögonblicks bilden av data på de skrivskyddade replikerna. När uppdateringarna har slutförts kan en Synch-åtgärd utföras för att se till att de skrivskyddade replikerna är aktuella.


## <a name="next-steps"></a>Nästa steg

[Asynkron uppdatering med REST API:et](analysis-services-async-refresh.md)  
[Utskalning för Azure Analysis Services](analysis-services-scale-out.md)  
[Hög tillgänglighet för Analysis Services](analysis-services-bcdr.md)  
[Vägledning för nytt försök för Azure-tjänster](/azure/architecture/best-practices/retry-service-specific)