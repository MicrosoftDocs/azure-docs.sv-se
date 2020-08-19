---
title: Introduktion till det inbyggda stödet för Jupyter Notebooks i Azure Cosmos DB (för hands version)
description: Lär dig hur du kan använda det inbyggda stödet för Jupyter Notebooks i Azure Cosmos DB för att interaktivt köra frågor.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 0299ab1e76e742adb8d2653b8b5d6923aff2f11f
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588164"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Inbyggt stöd för Jupyter Notebooks i Azure Cosmos DB (för hands version)

Jupyter Notebook är ett webbprogram med öppen källkod som gör det möjligt att skapa och dela dokument som innehåller kod, formler, visualiseringar och löpande text. 

Azure Cosmos DB inbyggda antecknings böcker för Jupyter är direkt integrerade i Azure Portal och dina Azure Cosmos DB-konton, vilket gör dem praktiska och enkla att använda. Utvecklare, data forskare, ingenjörer och analytiker kan använda välbekanta Jupyter Notebooks-upplevelse för att utföra data utforskning, data rensning, data transformationer, numeriska simuleringar, statistisk modellering, data visualisering och maskin inlärning.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Jupyter Notebooks-visualiseringar i Azure Cosmos DB":::

Azure Cosmos DB stöder både C#-och python-anteckningsböcker för alla API: er, inklusive Core (SQL), Cassandra, Gremlin, Table och API för MongoDB. I antecknings boken kan du dra nytta av inbyggda kommandon och funktioner som gör det enkelt att skapa Azure Cosmos DB resurser, ladda upp data och fråga och visualisera dina data i Azure Cosmos DB. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Stöd för Jupyter Notebooks i Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>Fördelar med Jupyter-anteckningsböcker

Jupyter-anteckningsböcker utvecklades ursprungligen för data vetenskaps program skrivna i python och R. De kan dock användas på olika sätt för olika typer av projekt, inklusive:

**Data visualisering:** Med Jupyter-anteckningsböcker kan du visualisera data i form av en delad antecknings bok som återger en data uppsättning som en bild. Du kan skapa visualiseringar, göra interaktiva ändringar i den delade koden och data uppsättningen och dela resultatet.

**Kod delning:** Tjänster som GitHub ger olika sätt att dela kod, men de är mycket icke-interaktiva. Med en Jupyter Notebook kan du Visa kod, köra den och visa resultaten direkt i Azure Portal.

**Live-interaktioner med kod:** Koden i ett Jupyter Notebook är dynamisk. Du kan redigera den och köra uppdateringarna stegvis i real tid. Du kan också bädda in användar kontroller (till exempel skjutreglage eller text inmatnings fält) som används som inmatnings källor för kod, demonstrationer eller proof of Concepts (POC).

**Dokumentation av kod exempel och resultat för data utforskning:** Om du har en kod i koden och du vill förklara rad för rad hur den fungerar kan du bädda in den i en Jupyter Notebook. Du kan lägga till interaktivitet tillsammans med dokumentationen på samma tid.

**Inbyggda kommandon för Azure Cosmos DB:** Azure Cosmos DB inbyggda Magic-kommandon gör det enkelt att interagera med ditt konto. Du kan använda kommandon som%% upload och%% SQL för att överföra data till en behållare och fråga den med [SQL API-syntax](sql-query-getting-started.md). Du behöver inte skriva ytterligare anpassad kod.

**Alla i en plats miljö:** Jupyter-anteckningsböcker kombinerar kod, RTF, bilder, videor, animeringar, matematiska ekvationer, ritytor, kartor, interaktiva figurer, widgetar och grafiska användar gränssnitt till ett enda dokument.

## <a name="components-of-a-jupyter-notebook"></a>Komponenter i en Jupyter Notebook

Jupyter-anteckningsböcker kan innehålla flera typer av komponenter, som var och en är ordnade i diskreta block eller celler:

**Text och HTML:** Oformaterad text eller text som är kommenterad i markdown-syntaxen för att generera HTML, kan infogas i dokumentet när som helst. CSS-format kan också inkluderas infogade eller läggas till i mallen som används för att skapa antecknings boken.

**Kod och utdata:** Jupyter Notebooks stöder python och C#-kod. Resultatet av den körda koden visas omedelbart efter kodblock och kodblock kan köras flera gånger i vilken ordning som helst.

**Visualiseringar:** Du kan generera grafik och diagram från koden med hjälp av moduler som matplotlib, ritas, bokeh och andra. På samma sätt som utdata visas dessa visualiseringar bredvid den kod som genererar dem. På samma sätt som utdata visas dessa visualiseringar bredvid den kod som genererar dem.

**Multimedia:** Eftersom Jupyter Notebooks bygger på webb teknik kan de Visa alla typer av multimedia som stöds av en webb sida. Du kan inkludera dem i en bärbar dator som HTML-element, eller så kan du generera dem program mässigt genom att använda `IPython.display` modulen.

**Data:** Du kan importera data från Azure Cosmos-behållare eller resultaten av frågor till en Jupyter Notebook program mässigt. Använd inbyggda Magic-kommandon för att överföra eller fråga efter data i Azure Cosmos DB. 

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med inbyggda Jupyter-anteckningsböcker i Azure Cosmos DB finns i följande artiklar:

* [Aktivera antecknings böcker i ett Azure Cosmos-konto](enable-notebooks.md)
* [Använda python-funktioner och-kommandon i python](use-python-notebook-features-and-commands.md)
* [Använda C#-funktioner och-kommandon i C#](use-csharp-notebook-features-and-commands.md)
