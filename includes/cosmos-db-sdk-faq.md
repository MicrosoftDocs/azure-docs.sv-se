---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515234"
---
**1. Hur kommer kunder att meddelas om att ta bort SDK?**

Microsoft kommer att tillhandahålla ett meddelande om 12 månader till slutet av stödet för att dra tillbaka SDK för att under lätta en smidig över gång till en SDK som stöds. Dessutom kommer kunderna att meddelas via olika kommunikations kanaler – Azure Portal, Azure-uppdateringar och direkt kommunikation till tilldelade tjänst administratörer.

**2. kan kunder skapa program med hjälp av en "att bli"-föråldrad Azure Cosmos DB SDK under perioden på 12 månader?** 

Ja, kunderna får fullständig åtkomst till författare, distribution och ändring av program med hjälp av "to-to-repensionered Azure Cosmos DB SDK under 12-månaders meddelande perioden. Under den 12 månaders uppsägnings perioden uppmanas kunderna att migrera till en nyare version av Azure Cosmos DB SDK som stöds. 

**3. efter indragnings datumet, vad händer med program som använder Azure Cosmos DB SDK som inte stöds?** 

Efter indragnings datumet kommer Azure Cosmos DB inte längre att göra fel korrigeringar, lägga till nya funktioner och ge stöd till de indragna SDK-versionerna. Om du föredrar att inte uppgradera fortsätter de begär Anden som skickas från de tillbakadragna versionerna av SDK: n att betjänas av tjänsten Azure Cosmos DB. 

**4. vilka SDK-versioner kommer att ha de senaste funktionerna och uppdateringarna?**

Nya funktioner och uppdateringar läggs bara till i den senaste lägre versionen av den senaste viktiga SDK-versionen som stöds. Vi rekommenderar att du alltid använder den senaste versionen för att dra nytta av nya funktioner, prestanda förbättringar och fel korrigeringar. Om du använder en gammal, icke-tillbakadragen version av SDK: er som begärs till Azure Cosmos DB fungerar fortfarande, men du kommer inte att ha åtkomst till några nya funktioner.  

**5. Vad ska jag göra om jag inte kan uppdatera mitt program innan ett avhuggs datum?**

Vi rekommenderar att du uppgraderar till den senaste SDK: n så tidigt som möjligt. När ett SDK har taggats för pensionering har du 12 månader på dig att uppdatera ditt program. Om du inte kan uppdatera efter indragnings datumet fortsätter förfrågningar som skickas från de indragna versionerna av SDK: n att betjänas av Azure Cosmos DB, så att dina program som körs fortsätter att fungera. Azure Cosmos DB kommer dock inte längre att göra fel korrigeringar, lägga till nya funktioner och tillhandahålla stöd för de indragna SDK-versionerna. 

Om du har en Support plan och behöver teknisk support kan du [kontakta oss](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) genom att skicka in ett support ärende.
    


