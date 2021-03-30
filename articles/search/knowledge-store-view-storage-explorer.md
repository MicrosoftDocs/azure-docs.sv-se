---
title: Visa ett kunskaps lager med Storage Explorer
titleSuffix: Azure Cognitive Search
description: Visa och analysera Azure Kognitiv sökning kunskaps lager med Azure Portals Storage Explorer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566017"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visa ett kunskaps lager med Storage Explorer

I den här artikeln lär du dig hur du kan ansluta till och utforska ett kunskaps lager med hjälp av Storage Explorer i Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

+ Följ stegen i [skapa ett kunskaps lager i Azure Portal](knowledge-store-create-portal.md) för att skapa ett exempel på kunskaps lager som används i den här genom gången.

+ Du kommer också att behöva namnet på det Azure Storage-konto som du använde för att skapa kunskaps lagret, tillsammans med dess åtkomst nyckel från Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Visa, redigera och fråga ett kunskaps lager i Storage Explorer

1. [Öppna det lagrings konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) som du använde för att skapa kunskaps lagret i Azure Portal.

1. I lagrings kontots vänstra navigerings fönster klickar du på **Storage Explorer**.

1. Expandera listan **tabeller** om du vill visa en lista över Azure Table-projektioner som skapades när du körde guiden **Importera data** på din hotell gransknings exempel data.

Välj en tabell för att visa de utförliga data, inklusive nyckel fraser och sentiment resultat.

   ![Visa tabeller i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visa tabeller i Storage Explorer")

Om du vill ändra data typen för ett tabell värde eller ändra enskilda värden i tabellen, klickar du på **Redigera**. När du ändrar data typen för en kolumn i en tabell rad, kommer den att tillämpas på alla rader.

   ![Redigera tabell i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Redigera tabell i Storage Explorer")

Om du vill köra frågor klickar du på **fråga** i kommando fältet och anger dina villkor.  

   ![Fråg Bell i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Fråg Bell i Storage Explorer")

## <a name="clean-up"></a>Rensa

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

Anslut det här kunskaps lagret till Power BI för djupare analys eller flytta framåt med kod med hjälp av REST API och Postman för att skapa ett annat kunskaps lager.

> [!div class="nextstepaction"]
> [Anslut till Power BI](knowledge-store-connect-power-bi.md) 
>  [Skapa ett kunskaps lager i rest](knowledge-store-create-rest.md)
