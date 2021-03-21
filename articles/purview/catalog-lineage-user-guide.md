---
title: Användar handbok för Data Catalog härkomst (för hands version)
description: Den här artikeln innehåller en översikt över funktionen Catalog härkomst i Azure avdelningens kontroll.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 47c5ad18f50bd25dd3bf351090ffc9ef5d54f0b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202917"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Användar handbok för Azure avdelningens kontroll Data Catalog härkomst

Den här artikeln innehåller en översikt över data härkomst-funktionerna i Azure avdelningens kontroll Data Catalog.

## <a name="background"></a>Bakgrund

En av plattforms funktionerna i Azure avdelningens kontroll är möjligheten att Visa härkomst mellan data uppsättningar som skapats av data processer. System som Data Factory, data resurs och Power BI fånga härkomst data när de flyttas. Anpassad härkomst-rapportering stöds också via Atlas krokar och REST API.

## <a name="lineage-collection"></a>Härkomst-samling 
 Metadata som samlas in i Azure-avdelningens kontroll från företags data system häftas över för att visa en slut punkt till slut punkt data härkomst. Data system som samlar in härkomst i avdelningens kontroll är i stort sett kategoriserade till följande tre typer.

### <a name="data-processing-system"></a>Data bearbetnings system
Data integrerings-och ETL-verktyg kan skicka härkomst till Azure avdelningens kontroll vid körning. Verktyg som Data Factory, data resurs, Synapse, Azure Databricks och så vidare, tillhör den här kategorin av data system. Data bearbetnings systemen refererar till data uppsättningar som källa från olika databaser och lagrings lösningar för att skapa mål data uppsättningar. Listan över data bearbetnings system som är integrerade med avdelningens kontroll för härkomst visas i tabellen nedan.


| Data bearbetnings system | Omfattning som stöds |
| ---------------------- | ------------|
| Azure Data Factory | [Kopierings aktivitet](how-to-link-azure-data-factory.md#data-factory-copy-activity-support) <br> [Data flödes aktivitet](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [Kör SSIS-paket-aktivitet](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [Dela ögonblicks bild](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Data lagrings system
Databaser & lagrings lösningar som SQL Server, Teradata och SAP har sökmotorer för att transformera data med hjälp av skript språket. Data härkomst från lagrade procedurer samlas in i avdelningens kontroll och häftas ihop med härkomst från andra system.

| Data lagrings system | Omfattning som stöds |
| ---------------------- | ------------|
| Teradata | Lagrade procedurer

### <a name="data-analytics--reporting-systems"></a>Data analys & rapporterings system
Data system som Azure ML och Power BI rapportera härkomst till Azure avdelningens kontroll. Dessa system använder data uppsättningarna från lagrings systemen och bearbetas genom sin meta-modell för att skapa BI-instrumentpanel, ML-experiment och så vidare.

| Data analys & rapporterings system | Omfattning som stöds |
| ---------------------- | ------------|
| Power BI | [Data uppsättningar, data flöden, rapporter & instrument paneler](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Kom igång med härkomst

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE1XVQS]

Härkomst i avdelningens kontroll innehåller data uppsättningar och processer. Data uppsättningar kallas även noder medan processer också kan kallas kanter:

* **Data uppsättning (Node)**: en data uppsättning (strukturerad eller ostrukturerad) som tillhandahålls som indata till en process. Till exempel är en SQL-tabell, Azure blob och filer (till exempel. csv och. xml) alla data uppsättningar. I avsnittet härkomst i avdelningens kontroll representeras data uppsättningar av rektangulära rutor.

* **Process (Edge)**: en aktivitet eller omvandling som utförs på en data uppsättning kallas för en process. Till exempel, ADF Copy-aktivitet, ögonblicks bild av data resurs och så vidare. I avsnittet härkomst i avdelningens kontroll representeras processerna av rundade rutor.

Följ stegen nedan för att få åtkomst till härkomst-information för en till gång i avdelningens kontroll:

1. Gå till [sidan för Azure avdelningens kontroll-konton](https://aka.ms/purviewportal)i Azure Portal.

1. Välj ditt Azure avdelningens kontroll-konto i listan och välj sedan **Starta avdelningens kontroll-konto** på sidan **Översikt** .

1. På **Start** sidan för Azure-avdelningens kontroll söker du efter ett data uppsättnings namn eller process namnet, t. ex. ADF-kopiering eller data flödes aktivitet. Och tryck sedan på RETUR.

1. Välj till gången från Sök resultaten och välj fliken **härkomst** .

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Skärm bild som visar hur du väljer fliken härkomst." border="true":::

## <a name="asset-level-lineage"></a>Härkomst för till gångs nivå

Azure avdelningens kontroll stöder härkomst på till gångs nivå för data uppsättningarna och processerna. Om du vill se härkomst för till gångs nivå går du till fliken **härkomst** för den aktuella till gången i katalogen. Välj noden aktuell datauppsättning till gång. Som standard visas listan över kolumner som hör till data i det vänstra fönstret.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Skärm bild som visar hur du väljer Visa kolumner på härkomst-Sidan" border="true":::

## <a name="dataset-column-lineage"></a>Härkomst för data mängds kolumn

Om du vill se härkomst för en data uppsättning på kolumn nivå går du till fliken **härkomst** för den aktuella till gången i katalogen och följer stegen nedan:

1. När du är på fliken härkomst i det vänstra fönstret markerar du kryss rutan bredvid varje kolumn som du vill visa i data härkomst.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Skärm bild som visar hur du väljer kolumner som ska visas på härkomst-sidan." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. Hovra över en markerad kolumn i det vänstra fönstret eller i data uppsättningen för härkomst-arbetsytan för att se kolumn mappningen. Alla kolumn instanser är markerade.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Skärm bild som visar hur du hovrar över ett kolumn namn för att markera kolumn flödet i en data härkomst-sökväg." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. Om antalet kolumner är större än vad som kan visas i den vänstra rutan använder du alternativet filter för att välja en angiven kolumn efter namn. Alternativt kan du använda musen för att bläddra i listan.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Skärm bild som visar hur du filtrerar kolumner efter kolumn namn på härkomst-sidan." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. Om arbets ytan härkomst innehåller fler noder och kanter använder du filtret för att välja data till gångar eller bearbeta noder efter namn. Alternativt kan du använda musen för att panorera runt fönstret härkomst.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Skärm bild som visar data till gång-noder efter namn på sidan härkomst." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. Använd växla i det vänstra fönstret för att markera listan över data uppsättningar på härkomst-arbetsytan. Om du inaktiverar växlingen visas alla till gångar som innehåller minst en av de markerade kolumnerna. Om du aktiverar växlingen visas endast data uppsättningar som innehåller alla kolumner.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Skärm bild som visar hur du använder växla för att filtrera listan över noder på härkomst-sidan." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>Härkomst för process kolumn
Data processen kan ta en eller flera indata-datauppsättningar för att skapa en eller flera utdata. I avdelningens kontroll är kolumn nivå härkomst tillgänglig för process-noder. 
1. Växla mellan data uppsättningar för indata och utdata från en listruta i panelen kolumner.
2. Välj kolumner från en eller flera tabeller för att se den härkomst som flödar från data uppsättningen data uppsättning till motsvarande utdata-datauppsättning.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="Skärm bild som visar kolumner härkomst i en processerver." lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>Bläddra till till gångar i härkomst
1. Välj **Växla till till gång** på en till gång för att visa motsvarande metadata från vyn härkomst. Det är ett effektivt sätt att bläddra till en annan till gång i katalogen från vyn härkomst.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Skärm bild som visar hur du väljer växla till till gång i en härkomst data till gång." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. Härkomst-arbetsytan kan bli komplex för populära data uppsättningar. För att undvika rörig visning visar standardvyn bara fem nivåer av härkomst för till gången i fokus. Resten av härkomst kan utökas genom att klicka på bubblorna på härkomst-arbetsytan. Data konsumenter kan också dölja till gångarna på arbets ytan som inte är av intresse. Du kan minska oredan genom att stänga av växlare **fler härkomst** längst upp på härkomst-arbetsytan. Den här åtgärden döljer alla bubblor på härkomst-arbetsytan.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Skärm bild som visar hur du växlar fler härkomst." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. Använd smarta knappar på härkomst-arbetsytan för att få en optimal vy över härkomst. Autolayout, zooma in och zooma in/ut, hel skärm och navigerings karta är tillgängliga för en fördjupad härkomst-upplevelse i katalogen.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Skärm bild som visar hur du väljer smarta knappar för härkomst." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Nästa steg

* [Länk till Azure Data Factory för härkomst](how-to-link-azure-data-factory.md)
* [Länk till Azure Data Share för härkomst](how-to-link-azure-data-share.md)