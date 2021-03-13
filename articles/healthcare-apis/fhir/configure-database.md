---
title: Konfigurera databas inställningar i Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar databas inställningar i Azure API för FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 3ce51141f20d2e032cb255f53efe3cb63882f1b2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019771"
---
# <a name="configure-database-settings"></a>Konfigurera databasinställningarna 

Azure API för FHIR använder databasen för att lagra data. Prestanda för den underliggande databasen beror på antalet enheter för programbegäran (RU) som valts under tjänst etableringen eller i databas inställningarna när tjänsten har etablerats.

Azure API för FHIR lånar begreppet ru: er från Cosmos DB (se enheter för [programbegäran i Azure Cosmos DB](../../cosmos-db/request-units.md)) när du anger prestanda för underliggande databaser. 

Data flödet måste tillhandahållas för att säkerställa att tillräckligt med system resurser är tillgängliga för din databas hela tiden. Hur många ru: er du behöver för ditt program beror på vilka åtgärder du utför. Åtgärder kan vara från enkel läsning och skrivning till mer komplexa frågor. 

> [!NOTE]
> Eftersom olika åtgärder förbrukar olika antal RU returnerar vi det faktiska antalet ru: er som förbrukas i alla API-anrop i svars huvudet. På så sätt kan du profilera antalet ru: er som används av ditt program.

## <a name="update-throughput"></a>Uppdatera data flöde

Om du vill ändra den här inställningen i Azure Portal navigerar du till ditt Azure API för FHIR och öppnar databas bladet. Ändra sedan det tillhandahållna data flödet till önskat värde beroende på dina prestanda behov. Du kan ändra värdet upp till högst 10 000 RU/s. Kontakta Azure-supporten om du behöver ett högre värde.

Om databas data flödet är större än 10 000 RU/s eller om data som lagras i databasen är mer än 50 GB, måste klient programmet kunna hantera fortsättnings-token. En ny partition skapas i databasen för varje data flödes ökning på 10 000 RU/s eller om mängden data som lagras är mer än 50 GB. Flera partitioner skapar ett svar på flera sidor där sid brytning implementeras med hjälp av fortsättnings-token.

> [!NOTE] 
> Högre värde innebär högre Azure API för FHIR-genomflöde och högre kostnad för tjänsten.

![Konfigurations Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du uppdaterar din ru: er för Azure API för FHIR. Lär dig mer om hur du konfigurerar Kundhanterade nycklar som en databas inställning:

>[!div class="nextstepaction"]
>[Konfigurera kundhanterade nycklar](customer-managed-key.md)

Eller så kan du distribuera ett fullständigt hanterat Azure API för FHIR:
 
>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)