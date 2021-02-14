---
title: Kopiera data stegvis med
description: Dessa självstudier visar hur du inkrementellt kopierar data från ett källdatalager till ett måldatalager. Den första kopierar data från en tabell.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: a27f91f8fe8b6016444ad2d1830f0cc2e702f0a6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384766"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Läsa in data stegvis från ett källdatalager till ett måldatalager

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I en dataintegrationslösning är stegvis inläsning av data (eller deltadata) efter den första fullständiga datainläsningen ett vanligt scenario. Självstudierna i det här avsnittet visar olika sätt att läsa in data inkrementellt med Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Deltadatainläsning från databas med vattenstämpel
I det här fallet definierar du en vattenstämpel i din källdatabas. En vattenstämpel är en kolumn som har den senast uppdaterade tidsstämpeln eller en stegvis ökande nyckel. Lösningen för deltainläsning läser in de ändrade data mellan en gammal och en ny vattenstämpel. Arbetsflödet för den här metoden illustreras i följande diagram: 

![Arbetsflöde för att använda en vattenstämpel](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Stegvisa instruktioner finns i följande självstudier: 
- [Kopiera data stegvis från en tabell i Azure SQL Database till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Kopiera data stegvis från flera tabeller i en SQL Server instans till Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

För mallar, se följande:
- [Deltakopiera med kontrolltabellen](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Inläsning av deltadata från SQL DB med tekniken Ändringsspårning
Tekniken för ändringsspårning är en enkel lösning i SQL Server och Azure SQL Database som tillhandahåller en effektiv ändringsspårningsmekanism för program. Det gör att ett program enkelt kan identifiera data som har infogats, uppdaterats eller tagits bort. 

Arbetsflödet för den här metoden illustreras i följande diagram:

![Arbetsflöde för att använda Ändringsspårning](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Stegvisa instruktioner finns i följande självstudie: <br/>
- [Kopiera data stegvis från Azure SQL Database till Azure Blob Storage med ändringsspårningsteknik](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Läsa in endast nya och ändrade filer med hjälp av LastModifiedDate
Du kan bara kopiera nya och ändrade filer genom att använda LastModifiedDate i mål arkivet. ADF genomsöker alla filer från käll arkivet, tillämpar fil filtret med deras LastModifiedDate och kopierar bara den nya och uppdaterade filen sedan senaste gången till mål lagret.  Tänk på att om du låter ADF Skanna enorma mängder filer, men bara kopiera några filer till målet, förväntar du dig fortfarande den långa varaktigheten på grund av att fil genomsökningen också tar lång tid.   

Stegvisa instruktioner finns i följande självstudie: <br/>
- [Kopiera stegvis nya och ändrade filer baserat på LastModifiedDate från Azure Blob Storage till Azure Blob Storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

För mallar, se följande:
- [Kopiera nya filer genom LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Läsa in endast nya filer med hjälp av tidspartitionerat mapp- eller filnamn.
Du kan kopiera endast nya filer, där filer eller mappar redan har tidspartitionerats med tidssektorinformation som en del av fil- eller mappnamnet (till exempel /åååå/mm/dd/fil.csv). Det är den mest effektiva metoden för att stegvis läsa in nya filer. 

Stegvisa instruktioner finns i följande självstudie: <br/>
- [Kopiera stegvis nya filer baserat på tidspartitionerat mapp- eller filnamn från Azure Blob Storage till Azure Blob Storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie: 

> [!div class="nextstepaction"]
>[Kopiera data stegvis från en tabell i Azure SQL Database till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
