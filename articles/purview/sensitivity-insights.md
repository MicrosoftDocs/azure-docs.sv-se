---
title: Känslighets etikett som rapporterar till dina data i Azure avdelningens kontroll med hjälp av avdelningens kontroll Insights
description: Den här instruktions guiden beskriver hur du visar och använder avdelningens kontroll känslighets etiketter för dina data.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: bb8ac82b2e59ec86db89c7eba0ce607fcfc0ac2d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676559"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Känslighets etikett insikter om dina data i Azure avdelningens kontroll

Den här instruktions guiden beskriver hur du kommer åt, visar och filtrerar säkerhets insikter som tillhandahålls av känslighets etiketter som används för dina data.

Data källor som stöds är: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, Azure SQL-hanterad instans, Amazon S3-buckets

I den här instruktions guiden lär du dig att:

> [!div class="checklist"]
> - Starta ditt avdelningens kontroll-konto från Azure.
> - Visa känslighets etiketter för insikter om dina data
> - Öka detalj nivån för mer känslighets märkning av data

> [!NOTE]
> Känslighets etiketter som finns på [Power BI till gångar](register-scan-power-bi-tenant.md) som genomsöks av avdelningens kontroll visas inte för närvarande i rapporten känslighets etiketter insikter. 
>
> Om du vill visa känslighets etiketter på Power BI till gångar kan du Visa till gången i [avdelningens kontroll Data Catalog](how-to-search-catalog.md).
> 
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med avdelningens kontroll Insights bör du kontrol lera att du har slutfört följande steg:

- Konfigurera dina Azure-resurser och fyll i relevanta konton med test data

- [Utökade Microsoft 365 känslighets etiketter till till gångar i Azure avdelningens kontroll](create-sensitivity-label.md)och skapade eller markerade etiketter som du vill använda för dina data.

- Konfigurera och slutför en genomsökning av test data i varje data källa. Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md) och [skapa en skannings regel uppsättning](create-a-scan-rule-set.md).

- Inloggad på avdelningens kontroll med konto med en [data läsare eller data curator-roll](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md) och [automatiskt etikettera dina data i Azure avdelningens kontroll](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Använda avdelningens kontroll-känslighet för att märka insikter

I avdelningens kontroll liknar klassificeringarna ämnes koder och används för att markera och identifiera data för en speciell typ som finns i din datafastighet under genomsökningen.

Med känslighets etiketter kan du ange hur känslig viss information som finns i din organisation. Ett enskilt projekt namn kan till exempel vara mycket konfidentiellt i din organisation, medan samma term inte är konfidentiell till andra organisationer. 

Klassificeringar matchas direkt, till exempel en social security number, som har en klassificering av **person nummer**. 

Känslighets etiketter tillämpas däremot när en eller flera klassificeringar och villkor hittas tillsammans. I det här sammanhanget refererar [villkoren](/microsoft-365/compliance/apply-sensitivity-label-automatically) till alla parametrar som du kan definiera för ostrukturerade data, till exempel **närhet till en annan klassificering** och **% konfidensgrad**. 

Avdelningens kontroll använder samma klassificeringar, även kallade [känsliga informations typer](/microsoft-365/compliance/sensitive-information-type-entity-definitions), som Microsoft 365. På så sätt kan du utöka dina befintliga känslighets etiketter i dina Azure avdelningens kontroll-tillgångar.

> [!NOTE]
> När du har skannat dina käll typer ger du **känslighets etiketter** insikter om några timmar för att avspegla de nya till gångarna.

**Visa känslighets etiketting Insights:**

1. Gå till start sidan för **Azure-avdelningens kontroll** .

1. På sidan **Översikt** går du till avsnittet **Kom igång** och väljer konto panelen **Starta avdelningens kontroll** .

1. I avdelningens kontroll väljer du meny alternativet **insikter** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: till vänster för att komma åt ditt **Insights** -fält.

1. I avsnittet **insikter** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: väljer du **känslighets etiketter** för att visa rapporten avdelningens kontroll **känslighets etiketting Insights** .

    > [!NOTE]
    > Om den här rapporten är tom kanske du inte har utökat dina känslighets etiketter till Azure-avdelningens kontroll. Mer information finns i [etikettera data automatiskt i Azure avdelningens kontroll](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Känslighet för att märka insikter" lightbox="media/insights/sensitivity-labeling-insights.png":::

   Den huvudsakliga **känslighets sidan för insikter** visar följande områden:

   |Område  |Beskrivning  |
   |---------|---------|
   |**Översikt över källor med känslighets etiketter**     |Visar paneler som innehåller: <br>– Antalet prenumerationer som finns i dina data. <br>– Antalet unika känslighets etiketter som används på dina data <br>– Antalet källor med känslighets etiketter som används <br>-Antalet filer och tabeller som hittades med känslighets etiketter som används|
   |**Främsta källor med märkta data (senaste 30 dagarna)**     | Visar trenden under de senaste 30 dagarna, av antalet källor med känslighets etiketter.       |
   |**Översta etiketter som tillämpas på alla källor**     |Visar de översta etiketterna som används för alla dina avdelningens kontroll-dataresurser. |
   |**Översta etiketter som används på filer**     |Visar de viktigaste känslighets etiketterna som används för filer i dina data.          |
   |**Översta etiketter som används på tabeller**     | Visar de viktigaste känslighets etiketterna som används för databas tabeller i dina data. |   
   |  **Märk aktivitet**  |  Visar separata diagram för filer och tabeller, som var och en visar antalet filer eller tabeller som har etiketter ATS under den valda tids ramen. <br>**Standard**: 30 dagar<br>Välj **tids** filtret ovanför graferna för att välja en annan tidsram som ska visas.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Specificering av känslighets etikett för insikter

I något av följande känslighets diagram för att **märka insikter** väljer du länken **Visa mer** för att öka detalj nivån.

- **Översta etiketter som tillämpas på alla källor**
- **Översta etiketter som används på filer**
- **Översta etiketter som används på tabeller**
- **Märka aktiviteter > märkta data**

Exempel:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Specificering av känslighets etikett" lightbox="media/insights/sensitivity-label-drilldown.png":::

Gör något av följande för att lära dig mer:

|Alternativ  |Beskrivning  |
|---------|---------|
|**Filtrera dina data**     |  Använd filtren ovanför rutnätet för att filtrera data som visas, inklusive etikett namn, prenumerations namn eller källtyp. <br><br>Om du inte är säker på det exakta etikett namnet kan du ange en del av eller hela namnet i rutan **Filtrera efter nyckelord** .       |
|**Sortera rutnätet** |Välj en kolumn rubrik för att sortera rutnätet efter kolumnen. | 
|**Redigera kolumner**     |  Om du vill visa fler eller färre kolumner i ditt rutnät väljer du **Redigera kolumner** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: och väljer sedan de kolumner som du vill visa eller ändra ordningen på.    <br><br>Välj en kolumn rubrik för att sortera rutnätet efter kolumnen.   |
|**Öka detalj nivån ytterligare**     | Om du vill öka detalj nivån till en speciell etikett väljer du ett namn i kolumnen **känslighets etikett** för att visa rapporten **etikett per källa** . <br><br>I den här rapporten visas data för den valda etiketten, inklusive käll namn, källtyp, prenumerations-ID och antalet klassificerade filer och tabeller.      |
|**Bläddra till till gångar**     |  Om du vill bläddra igenom de till gångar som hittas med en speciell etikett eller källa väljer du en eller flera etiketter eller källor, beroende på vilken rapport du visar, och väljer sedan **Bläddra till gångar** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: ovanför filtren. <br><br>Sök resultatet visar alla etiketterade till gångar som har hittats för det valda filtret.  Mer information finns i [Sök i Azure avdelningens kontroll Data Catalog](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Integrering av känslighets etiketter med Microsoft 365 kompatibilitet

Nära integrering med [Microsoft Information Protection](/microsoft-365/compliance/information-protection) som erbjuds i Microsoft 365 innebär att avdelningens kontroll gör det möjligt för direkta sätt att utöka insynen i din datafastighet och klassificera och etikettera data.

För att dina Microsoft 365 känslighets etiketter ska utökas till dina till gångar i Azure avdelningens kontroll, måste du aktivt aktivera Information Protection för Azure avdelningens kontroll i Microsoft 365 Compliance Center.

Mer information finns i [etikettera data automatiskt i Azure avdelningens kontroll](create-sensitivity-label.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om de här insikts rapporterna i Azure avdelningens kontroll:

- [Ord lista](glossary-insights.md)
- [Skanna insikter](scan-insights.md)
- [Klassificerings insikter](./classification-insights.md)
- [Fil namns insikter](file-extension-insights.md)
