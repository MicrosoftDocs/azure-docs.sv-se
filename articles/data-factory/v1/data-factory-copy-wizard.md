---
title: Kopiera data enkelt med guiden Kopiera – Azure
description: Lär dig mer om hur du använder Data Factory kopierings guiden för att kopiera data från data källor som stöds till mottagare.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f3686a22291e8595deabe3e207ce66744c3cdd82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376912"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Kopiera eller flytta data enkelt med Azure Data Factory kopiera guiden
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-dot-net.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder den aktuella versionen av Data Factory-tjänsten. 


Guiden Azure Data Factory kopiering är till för att under lätta processen med att mata in data, vilket vanligt vis är ett första steg i ett slut punkt till slut punkts scenario för data integrering. När du går igenom Azure Data Factory kopierings guiden behöver du inte förstå några JSON-definitioner för länkade tjänster, data uppsättningar och pipeliner. Men när du har slutfört alla steg i guiden skapar guiden automatiskt en pipeline för att kopiera data från den valda data källan till den valda destinationen. Dessutom hjälper kopierings guiden dig att validera data som matas in vid tidpunkten för redigering, vilket sparar mycket av din tid, särskilt när du matar in data för första gången från data källan. Starta guiden Kopiera genom att klicka på panelen **Kopiera data** på Start sidan för din data fabrik.

![Guiden Kopiera](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>En intuitiv guide för att kopiera data
Med den här guiden kan du enkelt flytta data från en mängd olika källor till destinationer på några minuter. När du har gått igenom guiden skapas en pipeline med en kopierings aktivitet automatiskt åt dig tillsammans med beroende Data Factory entiteter (länkade tjänster och data uppsättningar). Inga ytterligare steg krävs för att skapa pipelinen.   

![Välj datakälla](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> I [själv studie kursen om guiden i guiden](data-factory-copy-data-wizard-tutorial.md) finns stegvisa anvisningar för hur du skapar ett exempel på en pipeline för att kopiera data från en Azure-blob till en Azure SQL Database tabell. 
> 
> 

Guiden är utformad med Big data i åtanke från början. Det är enkelt och effektivt att redigera Data Factory pipelines som flyttar hundratals mappar, filer eller tabeller med hjälp av Kopiera data guiden. Guiden stöder följande tre funktioner: automatisk data förhands granskning, schema insamling och mappning och filtrering av data. 

## <a name="automatic-data-preview"></a>Automatisk data förhands granskning
Med kopierings guiden kan du granska en del av data från den valda data källan så att du kan kontrol lera om data det är rätt data som du vill kopiera. Om käll informationen finns i en textfil parsar guiden Kopiera text filen för att lära sig rad-och kolumn avgränsare, och schemat automatiskt. 

![Filformatinställningar](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Insamling och mappning av schema
Schemat för indata kan inte matcha schemat för utdata i vissa fall. I det här scenariot måste du mappa kolumner från käll schemat till kolumner från mål schemat. 

Kopierings guiden mappar automatiskt kolumner i käll schemat till kolumner i mål schemat. Du kan åsidosätta mappningarna genom att använda List rutorna (eller) och ange om en kolumn måste hoppas över när data kopieras.   

![Schemamappning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrera data
Med guiden kan du filtrera källdata och bara välja de data som behöver kopieras till data lagret för mål/mottagare. Filtrering minskar mängden data som ska kopieras till Sink-datalagret och ökar därför data flödet för kopierings åtgärden. Det är ett flexibelt sätt att filtrera data i en Relations databas med hjälp av SQL-frågespråket (eller) i en Azure Blob-mapp med hjälp av [Data Factory Functions och variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering av data i en databas
I exemplet använder SQL-frågan `Text.Format` funktionen och `WindowStart` variabeln. 

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

Klicka på knappen **Bläddra** för **filen eller mappen**, bläddra till någon av dessa mappar (till exempel 2016->03->01->02) och klicka på **Välj**. Du bör se `2016/03/01/02` i text rutan. Ersätt nu **2016** med **{Year}**, **03** med **{Month}**, **01** med **{Day}** och **02** med **{Hour}** och tryck på TABB. Du bör se List rutor för att välja formatet för dessa fyra variabler:

![Använda systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Som du ser i följande skärm bild kan du också använda en **anpassad** variabel och alla [format strängar som stöds](/dotnet/standard/base-types/custom-date-and-time-format-strings). Använd **bläddringsknappen** först om du vill välja en mapp med den strukturen. Ersätt sedan ett värde med **{Custom}** och tryck på TABB för att se text rutan där du kan skriva format strängen.     

![Använda anpassad variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Stöd för olika data-och objekt typer
Med hjälp av guiden Kopiera kan du effektivt flytta hundratals mappar, filer eller tabeller.

![Välj tabeller som data ska kopieras från](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Schemaläggnings alternativ
Du kan köra kopierings åtgärden en gång eller enligt ett schema (varje timme, varje dag och så vidare). Båda dessa alternativ kan användas för anslutningens bredd i lokal, moln-och lokal Skriv bords kopia.

En enstaka kopierings åtgärd gör det möjligt att flytta data från en källa till ett mål bara en gång. Den gäller för data av valfri storlek och alla format som stöds. Med den schemalagda kopian kan du kopiera data på en bestämd upprepning. Du kan använda avancerade inställningar (t. ex. återförsök, tids gräns och aviseringar) för att konfigurera den schemalagda kopian.

![Schemaläggnings egenskaper](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nästa steg
En snabb genom gång av hur du använder Data Factory kopierings guiden för att skapa en pipeline med kopierings aktivitet finns i [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).