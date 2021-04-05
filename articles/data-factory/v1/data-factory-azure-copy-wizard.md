---
title: Data Factory Azure Copy-guiden
description: Lär dig mer om hur du använder Data Factory Azure Copy-guiden för att kopiera data från data källor som stöds till mottagare.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393385"
---
# <a name="azure-data-factory-copy-wizard"></a>Guiden Azure Data Factory kopiering

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Guiden Azure Data Factory kopiering fören klar processen med att mata in data, vilket vanligt vis är ett första steg i ett slut punkt till slut punkts scenario för data integrering. När du går igenom Azure Data Factory kopierings guiden behöver du inte förstå några JSON-definitioner för länkade tjänster, data uppsättningar och pipeliner. Guiden skapar automatiskt en pipeline för att kopiera data från den valda data källan till det valda målet. Dessutom hjälper kopierings guiden dig att verifiera att data matas in vid tidpunkten för redigeringen. Detta sparar tid, särskilt när du matar in data för första gången från data källan. Starta guiden Kopiera genom att klicka på panelen **Kopiera data** på Start sidan för din data fabrik.

![Guiden Kopiera](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Utformad för Big data
Med den här guiden kan du enkelt flytta data från en mängd olika källor till destinationer på några minuter. När du har gått igenom guiden skapas en pipeline med en kopierings aktivitet automatiskt åt dig, tillsammans med beroende Data Factory entiteter (länkade tjänster och data uppsättningar). Inga ytterligare steg krävs för att skapa pipelinen.   

![Välj datakälla](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Stegvisa instruktioner för att skapa en exempel-pipeline för att kopiera data från en Azure-blob till en Azure SQL Database tabell finns i [själv studie kursen om guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Guiden är utformad med Big data i åtanke från Start, med stöd för olika data-och objekt typer. Du kan redigera Data Factory pipelines som flyttar hundratals mappar, filer eller tabeller. Guiden stöder automatisk data förhands granskning, schema insamling och mappning och data filtrering.

## <a name="automatic-data-preview"></a>Automatisk data förhands granskning
Du kan förhandsgranska en del av data från den valda data källan för att kontrol lera om data är det du vill kopiera. Om käll informationen finns i en textfil, kan du dessutom parsa text filen för att lära sig rad-och kolumn avgränsare och schema automatiskt i kopierings guiden.

![Filformatinställningar](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Insamling och mappning av schema
Schemat för indata kan inte matcha schemat för utdata i vissa fall. I det här scenariot måste du mappa kolumner från käll schemat till kolumner från mål schemat.

> [!TIP]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure Synapse Analytics, om tabellen inte finns i destinations lagret, Data Factory stödja automatisk tabell skapande med käll schema. Lär dig mer från [att flytta data till och från Azure Synapse Analytics med hjälp av Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).

Använd en nedrullningsbar listruta för att välja en kolumn från det käll schema som ska mappas till en kolumn i mål schemat. Kopierings guiden försöker förstå ditt mönster för kolumn mappning. Den använder samma mönster för resten av kolumnerna, så att du inte behöver markera varje kolumn separat för att slutföra schema mappningen. Om du vill kan du åsidosätta dessa mappningar genom att använda List rutorna för att mappa kolumnerna en i taget. Mönstret blir mer korrekt när du mappar fler kolumner. Kopierings guiden uppdaterar kontinuerligt mönstret och når i slut änden det högra mönstret för den kolumn mappning som du vill uppnå.     

![Schemamappning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrera data
Du kan filtrera källdata om du bara vill välja de data som behöver kopieras till mottagar data lagret. Filtrering minskar mängden data som ska kopieras till Sink-datalagret och ökar därför data flödet för kopierings åtgärden. Det är ett flexibelt sätt att filtrera data i en Relations databas med hjälp av SQL-frågespråket eller filer i en Azure Blob-mapp med hjälp av [Data Factory Functions och variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering av data i en databas
Följande skärm bild visar en SQL-fråga med hjälp av `Text.Format` funktionen och `WindowStart` variabeln.

![Validera uttryck](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrering av data i en Azure Blob-mapp
Du kan använda variabler i mappsökvägen för att kopiera data från en mapp som fastställs vid körning baserat på [Systemvariabler](data-factory-functions-variables.md#data-factory-system-variables). Variabler som stöds är: **{Year**}, **{Month}**, **{Day}**, **{Hour}**, **{Minute}** och **{Custom}**. Exempel: inputfolder/{year}/{month}/{Day}.

Anta att du har inmatade mappar i följande format:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicka på knappen **Bläddra** för **filen eller mappen**, bläddra till någon av dessa mappar (till exempel 2016->03->01->02) och klicka på **Välj**. Du bör se `2016/03/01/02` i text rutan. Ersätt nu **2016** med **{Year}**, **03** med **{Month}**, **01** med **{Day}** och **02** med **{Hour}** och tryck på **TABB** -tangenten. Du bör se List rutor för att välja formatet för dessa fyra variabler:

![Använda systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Som du ser i följande skärm bild kan du också använda en **anpassad** variabel och alla [format strängar som stöds](/dotnet/standard/base-types/custom-date-and-time-format-strings). Använd **bläddringsknappen** först om du vill välja en mapp med den strukturen. Ersätt sedan ett värde med **{Custom}** och tryck på **TABB** -tangenten för att se text rutan där du kan skriva format strängen.     

![Använda anpassad variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Schemaläggnings alternativ
Du kan köra kopierings åtgärden en gång eller enligt ett schema (varje timme, varje dag och så vidare). Båda dessa alternativ kan användas för anslutningens bredd i olika miljöer, inklusive lokalt, moln och lokal Skriv bords kopia.

En enstaka kopierings åtgärd gör det möjligt att flytta data från en källa till ett mål bara en gång. Den gäller för data av valfri storlek och alla format som stöds. Med den schemalagda kopian kan du kopiera data på en bestämd upprepning. Du kan använda avancerade inställningar (t. ex. återförsök, tids gräns och aviseringar) för att konfigurera den schemalagda kopian.

![Schemaläggnings egenskaper](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Felsökning

I det här avsnittet beskrivs vanliga fel söknings metoder för guiden Kopiera i Azure Data Factory.

> [!NOTE] 
> De här fel söknings tipsen gäller för guiden Kopiera i version 1 av Data Factory. För Data Factory v2, se fel söknings guide vid [fel sökning Azure Data Factory](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Felkod: det går inte att verifiera i guiden Kopiera

- **Symptom**: du får varnings meddelandet "Det gick inte att verifiera" i det första steget i kopierings guiden.
- **Orsaker**: Detta kan inträffa när alla tredjeparts cookies är inaktiverade.
- **Lösning**: 
    - Använd Internet Explorer eller Microsoft Edge-webbläsare.
    - Om du använder Chrome Browser följer du anvisningarna nedan om du vill lägga till cookies-undantag för *microsoftonline.com* och *Windows.net*.
        1.  Öppna Chrome-webbläsaren.
        2.  Klicka på SKIFT nyckel eller tre rader till höger (anpassa och kontrol lera Google Chrome).
        3.  Klicka på **Inställningar**.
        4.  Sök efter **cookies** eller gå till **Sekretess** under avancerade inställningar.
        5.  Välj **innehålls inställningar**.    
        6.  Cookies ska ställas in så att **lokala data kan anges (rekommenderas)**.
        7.  Klicka på **Hantera undantag**. Under **hostname-mönster** anger du följande och kontrollerar att **Tillåt** är inställd.
            - login.microsoftonline.com
            - login.windows.net
        8.  Stäng webbläsaren och starta om.
    - Om du använder Firefox webbläsare följer du anvisningarna nedan för att lägga till cookies-undantag.
        1. Från menyn Firefox går du till **verktyg**  >  **alternativ**.
        2. Under **Sekretess**  >  **historiken** kan du se att den aktuella inställningen **använder anpassade inställningar för historik**.
        3. I **acceptera cookies från tredje part** kan den aktuella inställningen bli **aldrig**. du bör sedan klicka på **undantag** till höger för att lägga till följande platser.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Stäng webbläsaren och starta om. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Felkod: det går inte att öppna inloggnings sidan och ange lösen ord

- **Symptom**: guiden Kopiera omdirigerar sidan till inloggnings sidan, men inloggnings sidan visas inte korrekt.
- **Orsaker**: det här problemet kan inträffa om du har ändrat nätverks miljön från kontors nät verket till hem nätverket. Det finns vissa cacheminnen i webbläsare. 
- **Lösning**: 
    1.  Stäng webbläsaren och försök igen. Gå till nästa steg om problemet fortfarande finns.   
    2.  Om du använder Internet Explorer-webbläsare försöker du öppna den i privat läge (tryck på "CTRL" + "Shift" + "P"). Om du använder Chrome-webbläsare försöker du öppna den i Incognito-läge (tryck på "CTRL" + "Shift" + "N"). Gå till nästa steg om problemet fortfarande finns. 
    3.  Försök att använda en annan webbläsare. 


## <a name="next-steps"></a>Nästa steg
En snabb genom gång av hur du använder Data Factory kopierings guiden för att skapa en pipeline med kopierings aktivitet finns i [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).