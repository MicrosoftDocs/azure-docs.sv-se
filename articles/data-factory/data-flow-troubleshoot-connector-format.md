---
title: Felsöka anslutningsproblem och formatproblem i mappning av dataflöden
description: Lär dig hur du felsöker problem med dataflöden som rör anslutning och format i Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/20/2021
ms.openlocfilehash: 1991436e2cc890b5c6a339f79e42536ced2fbd36
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739484"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Felsöka anslutningsproblem och formateringsproblem vid mappning av dataflöden i Azure Data Factory


Den här artikeln utforskar felsökningsmetoder relaterade till anslutning och format för att mappa dataflöden i Azure Data Factory (ADF).


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Stöd för anpassade scheman i källan

#### <a name="symptoms"></a>Symtom
När du vill använda ADF-dataflödet för att flytta eller överföra data från Cosmos DB/JSON till andra datalager kan vissa kolumner i källdata missas. 

#### <a name="cause"></a>Orsak 
För schemafria anslutningsappar (kolumnnummer, kolumnnamn och kolumndatatyp för varje rad kan vara olika när du jämför med andra) använder ADF som standard exempelrader (till exempel data från de översta 100 eller 1 000 raderna) för att härleda schemat, och det här uppskjutna resultatet används som ett schema för att läsa data. Så om dina datalager har extra kolumner som inte visas på exempelrader, läses inte data från dessa extra kolumner, flyttas eller överförs till mottagare för datalager.

#### <a name="recommendation"></a>Rekommendation
Om du vill skriva över standardbeteendet och lägga till ytterligare fält tillhandahåller ADF alternativ för att anpassa källschemat. Du kan ange ytterligare/saknade kolumner som kan saknas i schema-infer-result i dataflödeskällans projektion för att läsa data, och du kan använda något av följande alternativ för att ange det anpassade schemat. Alternativ **1 är vanligtvis** mer prioriterat.

- **Alternativ 1:** Jämfört med ursprungliga källdata som kan vara en stor fil, tabell eller container som innehåller miljontals rader med komplexa scheman kan du skapa en tillfällig tabell/container med några rader som innehåller alla kolumner som du vill läsa och sedan gå vidare till följande åtgärd: 

    1. Använd dataflödeskällans **felsökningsinställningar för** att importera **projektion** med exempelfiler/tabeller för att få det fullständiga schemat. Du kan följa stegen i följande bild:<br/>

        ![Skärmbild som visar den första delen av det första alternativet för att anpassa källschemat.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Välj **Felsökningsinställningar på** dataflödesarbetsytan.
         1. I popup-fönstret väljer du **Exempeltabell** under fliken **cosmosSource** och anger namnet på tabellen i **tabellblocket.**
         1. Spara **inställningarna** genom att välja Spara.
         1. Välj **Importera projektion**.<br/>  
    
    1. Ändra tillbaka **felsökningsinställningarna för** att använda källdatauppsättningen för återstående dataförflyttning/-transformering. Du kan gå vidare med stegen i följande bild:<br/>

        ![Skärmbild som visar den andra delen av det första alternativet för att anpassa källschemat.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Välj **Felsökningsinställningar på** dataflödesarbetsytan.
         1. I popup-fönstret väljer du **Källdatauppsättning** under **fliken cosmosSource.**
         1. Spara **inställningarna** genom att välja Spara.<br/>
    
    Därefter respekterar och använder ADF-dataflödeskörningen det anpassade schemat för att läsa data från det ursprungliga datalagret. <br/>

- **Alternativ 2:** Om du är bekant med schemat och DSL-språket för källdata kan du manuellt uppdatera dataflödeskällskriptet för att lägga till ytterligare/missade kolumner för att läsa data. Ett exempel visas i följande bild: 

    ![Skärmbild som visar det andra alternativet för att anpassa källschemat.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Nästa steg
Mer hjälp med felsökning finns i följande resurser:

*  [Felsöka mappning av dataflöden i Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktionsbegäranden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)