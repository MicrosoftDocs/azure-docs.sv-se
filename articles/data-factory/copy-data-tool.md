---
title: Kopiera data verktyget Azure Data Factory
description: Innehåller information om Kopiera data-verktyget i Azure Data Factory UI
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: yexu
ms.openlocfilehash: 18d61c7f51547a524e64fc44c98e70e0f5bc2752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373002"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Verktyget Kopiera data i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med verktyget Azure Data Factory Kopiera data kan du enkelt och optimera processen med att mata in data i en data Lake, som vanligt vis är ett första steg i ett slut punkt till slut punkts data integrerings scenario.  Det sparar tid, särskilt när du använder Azure Data Factory för att mata in data från en data källa för första gången. Några av fördelarna med att använda det här verktyget är:

- När du använder Azure Data Factory Kopiera data-verktyget behöver du inte förstå Data Factory definitioner för länkade tjänster, data uppsättningar, pipeliner, aktiviteter och utlösare. 
- Flödet av Kopiera data verktyg är intuitivt för att läsa in data i en data Lake. Verktyget skapar automatiskt alla nödvändiga Data Factory resurser för att kopiera data från det valda käll data lagret till det valda data lagret för destination/mottagare. 
- Med verktyget Kopiera data kan du verifiera de data som matas in vid tidpunkten för redigering, vilket hjälper dig att undvika eventuella fel i början.
- Om du behöver implementera komplex affärs logik för att läsa in data i en data Lake kan du fortfarande redigera Data Factory-resurser som skapats av Kopiera data-verktyget genom att använda redigering per aktivitet i Data Factory användar gränssnitt. 

Följande tabell ger vägledning om när du ska använda Kopiera data-verktyget eller redigering per aktivitet i Data Factory användar gränssnitt: 

| Kopiera data-verktyg | Redigering per aktivitet (kopierings aktivitet) |
| -------------- | -------------------------------------- |
| Du vill enkelt bygga en data inläsnings uppgift utan att lära dig om Azure Data Factory entiteter (länkade tjänster, data uppsättningar, pipeliner osv.) | Du vill implementera komplex och flexibel logik för att läsa in data till sjö. |
| Du vill snabbt läsa in ett stort antal data artefakter i ett data Lake. | Du vill kedja kopierings aktiviteten med efterföljande aktiviteter för rengöring eller bearbetning av data. |

Starta Kopiera data-verktyget genom att klicka på panelen **Kopiera data** på Start sidan för din data fabrik.

![Kom igång-sidan – länka till Kopiera data-verktyget](./media/doc-common-process/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitivt flöde för inläsning av data i en data Lake
Med det här verktyget kan du enkelt flytta data från en mängd olika källor till destinationer på några minuter med ett intuitivt flöde:  

1. Konfigurera inställningar för **källan**.
2. Konfigurera inställningar för **målet**. 
3. Konfigurera **Avancerade inställningar** för kopierings åtgärden, till exempel kolumn mappning, prestanda inställningar och inställningar för fel tolerans. 
4. Ange ett **schema** för data inläsnings aktiviteten. 
5. Granska **sammanfattningen** av Data Factory entiteter som ska skapas. 
6. **Redigera** pipelinen för att uppdatera inställningarna för kopierings aktiviteten vid behov. 

   Verktyget är utformat med Big data i åtanke från Start, med stöd för olika data-och objekt typer. Du kan använda den för att flytta hundratals mappar, filer eller tabeller. Verktyget har stöd för automatisk data förhands granskning, schema insamling och automatisk mappning och även data filtrering.

![Kopiera data-verktyg](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatisk data förhands granskning
Du kan förhandsgranska en del av data från det valda käll data lagret, vilket gör att du kan verifiera de data som kopieras. Om käll informationen finns i en textfil tolkar verktyget Kopiera data text filen för att automatiskt identifiera rad-och kolumn avgränsare och schema.

![Fil inställningar](./media/copy-data-tool/file-format-settings.png)

Efter identifieringen:

![Identifierade fil inställningar och för hands version](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Schema insamling och automatisk mappning
Schemat för data källan får inte vara detsamma som data målets schema i många fall. I det här scenariot måste du mappa kolumner från käll schemat till kolumner från mål schemat.

Verktyget Kopiera data övervakar och lär dig hur du mappar kolumner mellan käll-och mål lager. När du har valt en eller några få kolumner från käll data lagret och mappar dem till mål schemat, börjar verktyget Kopiera data att analysera mönstret för kolumn par som du har valt från båda sidor. Sedan tillämpas samma mönster på resten av kolumnerna. Därför kan du se att alla kolumner har mappats till målet på ett sätt som du vill precis efter flera klick.  Om du inte är nöjd med valet av kolumn mappning som tillhandahålls av Kopiera data-verktyget kan du ignorera det och fortsätta att mappa kolumnerna manuellt. Under tiden lär sig Kopiera data-verktyget hela tiden att lära sig och uppdatera mönstret och når slutligen det högra mönstret för den kolumn mappning som du vill uppnå. 

> [!NOTE]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure Synapse Analytics, om tabellen inte finns i mål lagret, kan Kopiera data verktyget skapa tabellen automatiskt med hjälp av käll schemat. 

## <a name="filter-data"></a>Filtrera data
Du kan filtrera källdata om du bara vill välja de data som behöver kopieras till mottagar data lagret. Filtrering minskar mängden data som ska kopieras till Sink-datalagret och ökar därför data flödet för kopierings åtgärden. Kopiera data-verktyget är ett flexibelt sätt att filtrera data i en Relations databas med hjälp av SQL-frågespråket eller filer i en Azure Blob-mapp. 

### <a name="filter-data-in-a-database"></a>Filtrera data i en databas
Följande skärm bild visar en SQL-fråga för att filtrera data.

![Filtrera data i en databas](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrera data i en Azure Blob-mapp
Du kan använda variabler i mappsökvägen för att kopiera data från en mapp. Variabler som stöds är: **{Year**}, **{Month}**, **{Day}**, **{Hour}** och **{minut**}. Exempel: inputfolder/{year}/{month}/{Day}. 

Anta att du har inmatade mappar i följande format: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicka på knappen **Bläddra** för **filen eller mappen**, bläddra till någon av dessa mappar (till exempel 2016->03->01->02) och klicka på **Välj**. Du bör se 2016/03/01/02 i text rutan. 

Ersätt sedan **2016** med **{Year}**, **03** med **{Month}**, **01** med **{Day}** och **02** med **{Hour}** och tryck på **TABB** -tangenten. Du bör se List rutor för att välja formatet för dessa fyra variabler:

![Filtrera fil eller mapp](./media/copy-data-tool/filter-file-or-folder.png)

Verktyget Kopiera data genererar parametrar med uttryck, funktioner och systemvariabler som kan användas för att representera {Year}, {Month}, {Day}, {Hour} och {minut} när pipelinen skapas.

## <a name="scheduling-options"></a>Schemaläggnings alternativ
Du kan köra kopierings åtgärden en gång eller enligt ett schema (varje timme, varje dag och så vidare). Dessa alternativ kan användas för anslutningarna i olika miljöer, inklusive lokalt, molnet och lokalt skriv bord. 

En enstaka kopierings åtgärd gör det möjligt att flytta data från en källa till ett mål bara en gång. Den gäller för data av valfri storlek och alla format som stöds. Med den schemalagda kopian kan du kopiera data på en upprepning som du anger. Du kan använda avancerade inställningar (t. ex. återförsök, tids gräns och aviseringar) för att konfigurera den schemalagda kopian.

![Schemaläggnings alternativ](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Nästa steg
Prova de här självstudierna som använder Kopiera data-verktyget:

- [Snabb start: skapa en data fabrik med hjälp av Kopiera data-verktyget](quickstart-create-data-factory-copy-data-tool.md)
- [Självstudie: kopiera data i Azure med hjälp av Kopiera data-verktyget](tutorial-copy-data-tool.md) 
- [Självstudie: Kopiera lokala data till Azure med hjälp av Kopiera data-verktyget](tutorial-hybrid-copy-data-tool.md)
