---
title: Batch-tjänstens arbetsflöde och resurser
description: Lär dig mer om funktionerna i batch-tjänsten och dess övergripande arbets flöde från en utvecklings synpunkt.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85965220"
---
# <a name="batch-service-workflow-and-resources"></a>Batch-tjänstens arbetsflöde och resurser

I den här översikten över kärn komponenterna i Azure Batchs tjänsten diskuterar vi arbets flödet på hög nivå som batch-utvecklare kan använda för att bygga storskaliga parallella beräknings lösningar, tillsammans med de primära tjänst resurser som används.

Oavsett om du utvecklar ett distribuerat beräknings program eller en tjänst som utfärdar direkta [REST API](/rest/api/batchservice/) -anrop eller om du använder en annan av [batch SDK: erna](batch-apis-tools.md#batch-service-apis), använder du många av de resurser och funktioner som beskrivs här.

> [!TIP]
> En mer översiktlig introduktion till Batch-tjänsten finns i [Grunderna i Azure Batch](batch-technical-overview.md). Se även de senaste [Batch-tjänstuppdateringarna](https://azure.microsoft.com/updates/?product=batch).

## <a name="basic-workflow"></a>Grundläggande arbetsflöde

Följande allmänna arbetsflöde är typiskt i praktiskt taget alla program och tjänster som använder Batch-tjänsten för att bearbeta parallella arbetsbelastningar:

1. Ladda upp **datafilerna** som du vill bearbeta till ett [Azure Storage](../storage/index.yml)-konto. Batch innehåller inbyggt stöd för åtkomst till Azure Blob Storage, och dina aktiviteter kan hämta dessa filer till [beräkningsnoder](nodes-and-pools.md#nodes) när aktiviteterna körs.
2. Ladda upp **programfilerna** som dina aktiviteter ska köra. Dessa filer kan vara binärfiler eller skript och deras beroenden, och körs av aktiviteterna i dina jobb. Dina aktiviteter kan hämta dessa filer från ditt lagringskonto, eller så kan du använda funktionen för [programpaket](nodes-and-pools.md#application-packages) i Batch för att hantera och distribuera program.
3. Skapa en [pool](nodes-and-pools.md#pools) med beräkningsnoder. När du skapar en pool anger du antalet beräkningsnoder för poolen, deras storlek och operativsystemet. När varje aktivitet i jobbet körs tilldelas det och körs på en av noderna i poolen.
4. Skapa ett [jobb](jobs-and-tasks.md#jobs). Ett jobb hanterar en samling aktiviteter. Du associerar varje jobb med en specifik pool där jobbets aktiviteter ska köras.
5. Lägg till [aktiviteter](jobs-and-tasks.md#tasks) till jobbet. Varje aktivitet kör programmet eller skriptet som du laddade upp för att bearbeta datafilerna som den hämtar från ditt lagringskonto. När en aktivitet är klar kan den överföra sina utdata till Azure Storage.
6. Övervaka jobbförloppet och hämta aktivitetsresultatet från Azure Storage.

> [!NOTE]
> Du behöver ett [Batch-konto](accounts.md) för att använda Batch-tjänsten. De flesta Batch-lösningar använder ett kopplat [Azure Storage](../storage/index.yml)-konto för fillagring och filhämtning.

## <a name="batch-service-resources"></a>Resurser i Batch-tjänsten

I följande avsnitt beskrivs de resurser i batch som möjliggör dina distribuerade beräknings scenarier.

- [Batch-konton och lagrings konton](accounts.md)
- [Noder och pooler](nodes-and-pools.md)
- [Jobb och uppgifter](jobs-and-tasks.md)
- [Filer och kataloger](files-and-directories.md)

## <a name="next-steps"></a>Nästa steg

- Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
- Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](quick-run-dotnet.md) eller [Python](quick-run-python.md). I de här snabbstarterna beskriver vi ett exempelprogram som använder Batch-tjänsten för att köra en arbetsbelastning på flera beräkningsnoder och förklarar hur du använder Azure Storage för mellanlagring och hämtning av filer i arbetsbelastningar.
- Hämta och installera [Batch Explorer](https://azure.github.io/BatchExplorer/) och använd verktyget i arbetet med att utveckla Batch-lösningar. Använd Batch Explorer för att skapa, felsöka och övervaka Azure Batch-program.
- Se community-resurser, inklusive [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), [batch-lagrings platsen](https://github.com/Azure/Batch)och [Azure Batch forum](/answers/topics/azure-batch.html).
