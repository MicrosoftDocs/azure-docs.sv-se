---
title: Kopiera program och data till pool-noder
description: Lär dig hur du kopierar program och data till pool-noder.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 0109171fd78dc11058daa30bf4604bebc1eeb857
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703655"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Kopiera program och data till pool-noder

Azure Batch stöder flera olika sätt att hämta data och program på datornoder så att de är tillgängliga för användning av uppgifter.

Vilken metod du väljer kan bero på omfattningen av din fil eller ditt program. Data och program kan behövas för att köra hela jobbet och måste därför installeras på varje nod. Vissa filer eller program kan krävas endast för en viss uppgift. Andra kan behöva installeras för jobbet, men behöver inte finnas på varje nod. Batch har verktyg för var och en av dessa scenarier.

## <a name="determine-the-scope-required-of-a-file"></a>Fastställer omfattningen som krävs för en fil

Du måste bestämma omfånget för en fil – är den fil som krävs för en pool, ett jobb eller en aktivitet. Filer som är begränsade till poolen bör använda poolprogram paket eller en start uppgift. Filer som omfattas av jobbet bör använda en jobb förberedelse aktivitet. Ett lämpligt exempel på filer som omfattas av poolen eller jobb nivån är program. Filer som är begränsade till aktiviteten bör använda resursfiler för aktiviteter.

## <a name="pool-start-task-resource-files"></a>Resurs fil för start aktivitet för pool

För program eller data som måste installeras på varje nod i poolen använder du resurs filens start aktivitet. Använd den här metoden tillsammans med antingen ett [programpaket](batch-application-packages.md) eller start aktivitetens resurs fil samling för att utföra ett installations kommando.  

Du kan till exempel använda kommando raden start uppgift för att flytta eller installera program. Du kan också ange en lista över filer eller behållare i ett Azure Storage-konto. Mer information finns i [Lägg till # ResourceFile i rest-dokumentationen](/rest/api/batchservice/pool/add#resourcefile).

Om alla jobb som körs i poolen kör ett program (. exe) som först måste installeras med en. msi-fil måste du ange start uppgiftens **vänta tills egenskapen lyckades** har **värdet sant**. Mer information finns i [Lägg till # StartTask i rest-dokumentationen](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Referenser för programpaket

Överväg att använda [programpaket](batch-application-packages.md)för program eller data som måste installeras på varje nod i poolen. Det finns inget installations kommando som är associerat med ett programpaket, men du kan använda en start uppgift för att köra ett installations kommando. Om programmet inte kräver installation eller består av ett stort antal filer, kan du använda den här metoden.

Programpaket är användbara när du har ett stort antal filer, eftersom de kan kombinera flera fil referenser till en liten nytto Last. Om du försöker att inkludera fler än 100 separata resursfiler i en aktivitet kan batch-tjänsten komma fram till interna system begränsningar för en enskild uppgift. Programpaket är också användbara när du har många olika versioner av samma program och behöver välja mellan dem.

## <a name="job-preparation-task-resource-files"></a>Resurs fil för jobb förberedelse aktivitet

För program eller data som måste installeras för att jobbet ska kunna köras, men som inte behöver installeras i hela poolen, bör du överväga att använda [jobb förberedelse aktivitetens resursfiler](./batch-job-prep-release.md).

Om din pool till exempel har många olika typer av jobb, och endast en jobbtyp behöver en. msi-fil för att kunna köras, är det bra att ställa in installations steget i en jobb förberedelse uppgift.

## <a name="task-resource-files"></a>Resurs fil för aktivitet

Aktivitets resursens filer är lämpliga när ditt program eller dina data endast är relevanta för en enskild uppgift.

Du kan till exempel ha fem aktiviteter, varje bearbetning av en annan fil och sedan skriva utdata till Blob Storage i det här fallet, indatafilen ska anges i samlingen med aktivitets resurs filer, eftersom varje aktivitet har sin egen indatafil.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Ytterligare sätt att hämta data till noder

Eftersom du har kontroll över Azure Batch noder och kan köra anpassade körbara filer, kan du hämta data från valfritt antal anpassade källor. Kontrol lera att batch-noden har anslutning till målet och att du har autentiseringsuppgifter för den källan på noden.

Några exempel på sätt att överföra data till batch-noder är:

- Hämtar data från SQL
- Hämta data från andra webb tjänster/anpassade platser
- Mappa en nätverks resurs

## <a name="azure-storage"></a>Azure-lagring

Tänk på att Blob Storage har laddat ned skalbarhets mål. Skalbarhet för Azure Storage-filresursen är desamma som för en enda blob. Storleken påverkar antalet noder och pooler som du behöver.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att använda [programpaket med batch](batch-application-packages.md).
- Lär dig mer om att [arbeta med noder och pooler](nodes-and-pools.md).
