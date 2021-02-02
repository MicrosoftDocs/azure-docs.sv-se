---
title: Använda åter givnings funktioner
description: Använda Azure Batch åter givnings funktioner. Försök att använda Batch Explorer programmet, antingen direkt eller anropas från ett klient program-plugin-program.
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: a5bf4d0ed2173d048d968f60dea72a1a0c25ab05
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429793"
---
# <a name="using-azure-batch-rendering"></a>Använda Azure Batch åter givning

Det finns flera sätt att använda Azure Batch åter givning:

* API:er:
  * Skriv kod med någon av batch-API: erna.  Utvecklare kan integrera Azure Batch funktioner i befintliga program eller arbets flöden, oavsett om de är molnbaserade eller baserade lokalt.
* Kommando rads verktyg:
  * Du kan använda [Azures kommando rad](/cli/azure/) eller [PowerShell](/powershell/azure/) för att använda skript för batch-användning.
  * I synnerhet gör [stödet för batch CLI](./batch-cli-templates.md) det mycket enklare att skapa pooler och skicka jobb.
* Batch Explorer användar gränssnitt:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) är ett plattforms oberoende klient verktyg som också tillåter att batch-konton hanteras och övervakas.
  * För varje åter givnings program finns ett antal pooler och jobbmallar som kan användas för att enkelt skapa pooler och skicka jobb.  En uppsättning mallar visas i program gränssnittet, med de mallfiler som används från GitHub.
  * Anpassade mallar kan skapas från början eller de tillhandahållna mallarna från GitHub kan kopieras och ändras.
* Plugin-program för klient program:
  * Plugin-program är tillgängliga som tillåter att batch-rendering används direkt i klientens design-och modellerings program.  Plugin-programmen anropar huvudsakligen Batch Explorer program med sammanhangsbaserad information om den aktuella 3D-modellen och innehåller funktioner som hjälper dig att hantera till gångar.

Det bästa sättet att prova Azure Batch åter givning och det enklaste sättet för slutanvändare, som inte är utvecklare och inte Azure-experter, är att använda det Batch Explorer programmet, antingen direkt eller anropas från ett plugin-program för klient program.

## <a name="using-batch-explorer"></a>Använda Batch Explorer

En steg-för-steg-guide om hur du använder Batch Explorer för att utföra åter givning finns i [själv studie kursen för över gångar](./tutorial-rendering-batchexplorer-blender.md).

### <a name="download-and-install"></a>Hämta och installera

Batch Explorer [hämtningar är tillgängliga](https://azure.github.io/BatchExplorer/) för Windows, OSX och Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Använda mallar för att skapa pooler och köra jobb

En omfattande uppsättning mallar är tillgängliga för användning med Batch Explorer som gör det enkelt att skapa pooler och skicka jobb för de olika åter givnings programmen utan att behöva ange alla egenskaper som krävs för att skapa pooler, jobb och uppgifter direkt med batch.  Mallarna som är tillgängliga i Batch Explorer lagras och visas i [en GitHub-lagringsplats](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Batch Explorer Galleri](./media/batch-rendering-using/batch-explorer-gallery.png)

Mallar tillhandahålls för alla program som finns på Marketplace återge VM-avbildningar.  För varje program finns det flera mallar, inklusive mallar för pool för processor-och GPU-pooler, Windows-och Linux-pooler. jobbmallar innehåller hel ram eller sida vid sida med blandnings åter givning och V-Ray-distribuerad åter givning. Uppsättningen med tillhandahållna mallar kommer att utökas med tiden för att tillgodose andra batch-funktioner, till exempel automatisk skalning av pooler.

Det är också möjligt för anpassade mallar att skapas, från början eller genom att ändra de angivna mallarna. Du kan använda anpassade mallar genom att välja objektet "lokala mallar" i avsnittet "Galleri" i Batch Explorer.

### <a name="file-system-and-data-movement"></a>Fil system och data förflyttning

Avsnittet "data" i Batch Explorer tillåter att filer kopieras mellan ett lokalt fil system och Azure Storages konton.

## <a name="next-steps"></a>Nästa steg

Exempel på batch-åter givning testar de två självstudierna:

* [Rendering med hjälp av Azure CLI](./tutorial-rendering-cli.md)
* [Rendering med hjälp av Batch Explorer](./tutorial-rendering-batchexplorer-blender.md)
