---
title: Klassificerings rapportering för dina data i Azure avdelningens kontroll med hjälp av avdelningens kontroll Insights
description: Den här instruktions guiden beskriver hur du visar och använder avdelningens kontroll klassificerings rapportering på dina data.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b6ea3e762ad098e373baa8487d8926105820f226
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666513"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Klassificera insikter om dina data från Azure avdelningens kontroll

Den här instruktions guiden beskriver hur du kommer åt, visar och filtrerar avdelningens kontroll klassificerings rapporter för dina data.

Data källor som stöds är: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure Synapse Analytics (tidigare SQL DW), Azure SQL Database, Azure SQL-hanterad instans, SQL Server, Amazon S3-buckets

I den här instruktions guiden lär du dig att:

> [!div class="checklist"]
> - Starta ditt avdelningens kontroll-konto från Azure
> - Visa klassificerings insikter om dina data
> - Öka detalj nivån för mer klassificerings information om dina data

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med avdelningens kontroll Insights bör du kontrol lera att du har slutfört följande steg:

- Konfigurera dina Azure-resurser och fyll i relevanta konton med test data

- Konfigurera och slutför en genomsökning av test data i varje data källa. Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md) och [skapa en skannings regel uppsättning](create-a-scan-rule-set.md).

- Inloggad på avdelningens kontroll med konto med en [data läsare eller data curator-roll](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Använda avdelningens kontroll klassificerings insikter

I Azure avdelningens kontroll liknar klassificeringarna ämnes koder och används för att markera och identifiera data för en speciell typ som finns i din datafastighet under genomsökningen.

Avdelningens kontroll använder samma känsliga informations typer som Microsoft 365, så att du kan sträcka ut dina befintliga säkerhets principer och skydd över hela data fastigheten.

> [!NOTE]
> När du har skannat dina käll typer ger du **klassificerings** insikter några timmar för att avspegla de nya till gångarna.

**Så här visar du klassificerings insikter:**

1. Gå till skärmen **Azure avdelningens kontroll** [instance i Azure Portal](https://aka.ms/purviewportal) och välj ditt avdelningens kontroll-konto.

1. På sidan **Översikt** går du till avsnittet **Kom igång** och väljer konto panelen **Starta avdelningens kontroll** .

1. I avdelningens kontroll väljer du meny alternativet **insikter** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: till vänster för att komma åt ditt **Insights** -fält.

1. I avsnittet **insikter** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: väljer du **klassificering** för att visa rapporten avdelningens kontroll **klassificerings Insights** .

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Rapport över klassificerings Insights" lightbox="media/insights/select-classification-labeling.png":::

   På huvud sidan för **klassificerings** information visas följande områden:

   |Område  |Beskrivning  |
   |---------|---------|
   |**Översikt över källor med klassificeringar**     |Visar paneler som innehåller: <br>– Antalet prenumerationer som har hittats i dina data <br>– Antalet unika klassificeringar som finns i dina data <br>-Antalet klassificerade källor hittades <br>-Antalet klassificerade filer som hittades <br>-Antalet klassificerade tabeller hittades         |
   |**Främsta källor med klassificerade data (senaste 30 dagarna)**     |Visar trenden under de senaste 30 dagarna, av det antal källor som hittades med klassificerade data.            |
   |**Främsta klassificerings kategorier efter källor**     |Visar antalet källor som hittas av klassificerings kategorin, till exempel **ekonomi** eller **myndigheter**.      |
   |**Top-klassificeringar för filer**     |Visar de viktigaste klassificeringarna som tillämpas på filer i dina data, t. ex. kreditkorts nummer eller nationella identifierings nummer.         |
   |**Främsta klassificeringar för tabeller**     | Visar de viktigaste klassificeringarna som tillämpas på tabeller i dina data, till exempel personlig identifierings information. |   
   |  **Klassificerings aktivitet** <br>(filer och tabeller) |  Visar separata diagram för filer och tabeller, som var och en visar antalet filer eller tabeller som klassificerats under den valda tids ramen. <br>**Standard**: 30 dagar<br>Välj **tids** filtret ovanför graferna för att välja en annan tidsram som ska visas.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Nedbrytning av klassificerings Insights

I något av följande diagram över **klassificerings Insights** väljer du länken **Visa mer** för att öka detalj nivån för mer information:

- **Främsta klassificerings kategorier efter källor**
- **Top-klassificeringar för filer**
- **Främsta klassificeringar för tabeller**
- **Klassificerings aktiviteter > klassificerings data**

Exempel:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Visa alla klassificeringar" lightbox="media/insights/view-classifications.png":::

Gör något av följande för att lära dig mer:

|Alternativ  |Beskrivning  |
|---------|---------|
|**Filtrera dina data**     |  Använd filtren ovanför rutnätet för att filtrera data som visas, inklusive klassificerings namn, prenumerations namn eller källtyp. <br><br>Om du inte är säker på det exakta klassificerings namnet kan du ange en del av eller hela namnet i rutan **Filtrera efter nyckelord** .       |
|**Sortera rutnätet** |Välj en kolumn rubrik för att sortera rutnätet efter kolumnen. | 
|**Redigera kolumner**     |  Om du vill visa fler eller färre kolumner i ditt rutnät väljer du **Redigera kolumner** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: och väljer sedan de kolumner som du vill visa eller ändra ordningen på.   |
|**Öka detalj nivån ytterligare**     | Om du vill öka detalj nivån till en speciell klassificering väljer du ett namn i kolumnen **klassificering** för att visa rapporten **klassificering per källa** . <br><br>I den här rapporten visas data för den valda klassificeringen, inklusive källans namn, källtyp, prenumerations-ID och antalet klassificerade filer och tabeller.      |
|**Bläddra till till gångar**     |  Om du vill bläddra igenom de till gångar som finns med en angiven klassificering eller källa väljer du en klassificering eller källa, beroende på vilken rapport du visar, och väljer sedan **Bläddra till gångar** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: ovanför filtren. <br><br>Sök resultatet visar alla klassificerade till gångar som hittades för det valda filtret.  Mer information finns i [Sök i Azure avdelningens kontroll Data Catalog](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure avdelningens kontroll Insight-rapporter
> [!div class="nextstepaction"]
> [Ord lista](glossary-insights.md)

> [!div class="nextstepaction"]
> [Skanna insikter](scan-insights.md)

> [!div class="nextstepaction"]
> [Känslighet för att märka insikter](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Fil namns insikter](file-extension-insights.md)