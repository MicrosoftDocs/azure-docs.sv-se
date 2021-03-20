---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90068751"
---
**Hur meddelas jag om tillbakadragningen av SDK?**

För att säkerställa en smidig övergång till en SDK-version som stöds meddelar Microsoft kunder 12 månader innan stödet för den äldre SDK:n upphör. Vi meddelar dig via flera kommunikationskanaler: på Azure-portalen, genom Azure-uppdateringar och via direktkommunikation till tjänstadministratörer.

**Kan jag skapa program med en SDK för Azure Cosmos DB som kommer att dras tillbaka inom 12 månader?** 

Ja. Du kan skapa, distribuera och ändra program med Azure Cosmos DB-SDK:n som kommer att dras tillbaka under 12-månadsperioden. Vi rekommenderar att du migrerar till en senare version av SDK:n för Azure Cosmos DB under 12-månadsperioden. 

**Vad händer med program som använder den Azure Cosmos DB-SDK som inte längre stöds efter tillbakadragningsdatumet?** 

Efter tillbakadragningsdatumet kommer Azure Cosmos DB inte att erbjuda buggkorrigeringar, nya funktioner eller stöd för de SDK-versioner som har dragits tillbaka. Om du föredrar att inte uppgradera kommer begäranden från de inaktuella versionerna av SDK:n att fortsätta att hanteras av tjänsten Azure Cosmos DB. 

**Vilka SDK-versioner har de senaste funktionerna och uppdateringarna?**

Nya funktioner och uppdateringar läggs bara till i den senaste delversionen av den senaste större SDK-versionen som stöds. Vi rekommenderar att du alltid använder den senaste versionen för att dra nytta av nya funktioner, prestandaförbättringar och felkorrigeringar. Om du använder en gammal, icke-tillbakadragen version av SDK:n, kommer dina begäranden till Azure Cosmos DB fortfarande att fungera, men du kommer inte att ha tillgång till nya funktioner.  

**Vad gör jag om jag inte har möjlighet att uppdatera mitt program före det sista datumet?**

Vi rekommenderar att du uppgraderar till den senaste SDK:n så tidigt som möjligt. När en SDK-version har flaggats för tillbakadragning har du 12 månader på dig att uppdatera ditt program. Om du inte har möjlighet att uppdatera före tillbakadragningsdatumet kommer begäranden som skickas från de tillbakadragna versionerna av SDK:n att fortsätta att betjänas av Azure Cosmos DB, och dina aktiva program kommer att fortsätta att fungera. Men Azure Cosmos DB kommer inte längre att erbjuda felkorrigeringar, nya funktioner eller stöd för de tillbakadragna SDK-versionerna. 

Om du har en supportplan och behöver teknisk support [kontaktar du oss](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) genom att skicka in en supportbegäran.
    


