---
title: Anslut funktioner till andra Azure-tjänster
description: Lär dig hur du lägger till bindningar som ansluter till andra Azure-tjänster till en befintlig funktion i ditt Azure Functions-projekt.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258209"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>Ansluta funktioner till Azure-tjänster med hjälp av bindningar

När du skapar en funktion läggs språkspecifik utlösnings kod till i projektet från en uppsättning utlösare. Om du vill ansluta din funktion till andra tjänster med hjälp av indata eller utgående bindningar måste du lägga till vissa bindnings definitioner i din funktion. Mer information om bindningar finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

## <a name="local-development"></a>Lokal utveckling       

När du utvecklar funktioner lokalt måste du uppdatera funktions koden för att lägga till bindningar. Med Visual Studio Code kan du göra det enklare att lägga till bindningar till en funktion.  

### <a name="visual-studio-code"></a>Visuell Studio-kod

När du använder Visual Studio Code för att utveckla din funktion och din funktion använder en function.jspå filen, kan Azure Functions-tillägget automatiskt lägga till en bindning till en befintlig function.jsi filen. Läs mer i [lägga till indata och utgående bindningar](functions-develop-vs-code.md#add-input-and-output-bindings).   

### <a name="manually-add-bindings-based-on-examples"></a>Lägg till bindningar manuellt baserat på exempel

När du lägger till en bindning till en befintlig funktion måste du uppdatera både funktions koden och function.jsi konfigurations filen, om de används av ditt språk. Både .NET-klass bibliotek och Java-funktioner använder attribut i stället för function.jspå, så du måste uppdatera i stället.

Använd följande tabell för att hitta exempel på olika typer av bindningar som du kan använda för att få hjälp med att uppdatera en befintlig funktion. Först väljer du fliken språk som motsvarar ditt projekt. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure Portal

När du utvecklar dina funktioner i [Azure Portal](https://portal.azure.com)lägger du till indata och utgående bindningar på fliken **integrera** för en specifik funktion. De nya bindningarna läggs till i antingen function.jspå filen eller till attributet attribut, beroende på ditt språk. Följande artiklar innehåller exempel på hur du lägger till bindningar till en befintlig funktion i portalen:

+ [Utgående bindning för Queue Storage](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB utgående bindning](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Nästa steg

+ [Utlösare och bindningar i Azure Functions](functions-triggers-bindings.md)
