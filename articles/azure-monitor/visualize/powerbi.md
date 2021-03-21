---
title: Importera Azure Log Analytics-data till Power BI | Microsoft Docs
description: Power BI är en molnbaserad tjänst för företags analys från Microsoft som tillhandahåller omfattande visualiseringar och rapporter för analys av olika data uppsättningar.  I den här artikeln beskrivs hur du konfigurerar och importerar Log Analytics data till Power BI och konfigurerar den för automatisk uppdatering.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 69e953f6d2cb9542a589fb2e282ff166c43fc0a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043617"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importera Azure Monitor loggdata till Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är en molnbaserad tjänst för företags analys från Microsoft som tillhandahåller omfattande visualiseringar och rapporter för analys av olika data uppsättningar.  Du kan importera resultatet av en Azure Monitor logg fråga till en Power BI data uppsättning så att du kan dra nytta av dess funktioner som att kombinera data från olika källor och dela rapporter på webben och mobila enheter.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Översikt
Om du vill importera data från en [Log Analytics arbets yta](../logs/manage-access.md) i Azure Monitor till Power BI skapar du en data uppsättning i Power BI baserat på en [logg fråga](../logs/log-query-overview.md) i Azure Monitor.  Frågan körs varje gången data uppsättningen uppdateras.  Sedan kan du bygga Power BI rapporter som använder data från data uppsättningen.  Om du vill skapa data uppsättningen i Power BI exporterar du din fråga från Log Analytics till [Power Query (M) språk](/powerquery-m/power-query-m-language-specification).  Sedan kan du använda den för att skapa en fråga i Power BI Desktop och sedan publicera den till Power BI som en data uppsättning.  Informationen för den här processen beskrivs nedan.

![Log Analytics till Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Exportera fråga
Börja med att skapa en [logg fråga](../logs/log-query-overview.md) som returnerar de data som du vill fylla i Power BI data uppsättningen.  Sedan kan du exportera frågan till [Power Query (M) språk](/powerquery-m/power-query-m-language-specification) som kan användas av Power BI Desktop.

> [!WARNING]
> Var noga med att [optimera din fråga](../logs/query-optimization.md) så att den inte tar alltför lång tid att köra eller tids gränsen. Observera **TimeSpan** -värdet i den exporterade frågan som definierar TimeSpan för de data som frågan ska hämta. Använd det minsta TimeSpan som du behöver för att begränsa mängden data som frågan returnerar.

1. [Skapa logg frågan i Log Analytics](../logs/log-analytics-tutorial.md) för att extrahera data för din data uppsättning.
2. Välj **Exportera**  >  **Power BI fråga (M)**.  Detta exporterar frågan till en textfil med namnet **PowerBIQuery.txt**. 

    ![Exportera loggs ökning](media/powerbi/export-analytics.png)

3. Öppna text filen och kopiera dess innehåll.

## <a name="import-query-into-power-bi-desktop"></a>Importera fråga till Power BI Desktop
Power BI Desktop är ett Skriv bords program där du kan skapa data uppsättningar och rapporter som kan publiceras till Power BI.  Du kan också använda den för att skapa en fråga med hjälp av Power Query språk som exporter ATS från Azure Monitor. 

1. Installera [Power BI Desktop](https://powerbi.microsoft.com/desktop/) om du inte redan har det och öppna programmet.
2. Välj **Hämta data**  >  **Tom fråga** för att öppna en ny fråga.  Välj **avancerad redigerare** och klistra in innehållet i den exporterade filen i frågan. Klicka på **Klar**.

    ![Power BI Desktop fråga](media/powerbi/desktop-new-query.png)

5. Frågan körs och resultatet visas.  Du kan uppmanas att ange autentiseringsuppgifter för att ansluta till Azure.  
6. Skriv ett beskrivande namn för frågan.  Standardvärdet är **Fråga1**. Klicka på **Stäng och tillämpa** för att lägga till data uppsättningen i rapporten.

    ![Power BI Desktop namn](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicera till Power BI
När du publicerar till Power BI skapas en data uppsättning och en rapport.  Om du skapar en rapport i Power BI Desktop kommer detta att publiceras med dina data.  Annars skapas en tom rapport.  Du kan ändra rapporten i Power BI eller skapa en ny som baseras på data uppsättningen.

1. Skapa en rapport som baseras på dina data.  Använd [Power BI Desktop-dokumentationen](/power-bi/desktop-report-view) om du inte är bekant med den.  
1. När du är redo att skicka den till Power BI klickar du på **publicera**.  
1. När du uppmanas väljer du ett mål i Power BI-kontot.  Om du inte har ett särskilt mål i åtanke använder du **min arbets yta**.

    ![Power BI Desktop publicera](media/powerbi/desktop-publish.png)

1. När publiceringen är klar klickar du på **Öppna i Power BI** för att öppna Power BI med din nya data uppsättning.


### <a name="configure-scheduled-refresh"></a>Konfigurera schemalagd uppdatering
Den data uppsättning som skapades i Power BI kommer att ha samma data som du tidigare såg i Power BI Desktop.  Du måste uppdatera data uppsättningen regelbundet för att köra frågan igen och fylla den med den senaste informationen från Azure Monitor.  

1. Klicka på den arbets yta där du laddade upp rapporten och välj menyn **data uppsättningar** . 
1. Välj snabb menyn bredvid din nya data uppsättning och välj **Inställningar**. 
1. Under **autentiseringsuppgifter för data källa** bör du ha ett meddelande om att autentiseringsuppgifterna är ogiltiga.  Detta beror på att du inte har angett några autentiseringsuppgifter än den data uppsättning som ska användas när data uppdateras.  
1. Klicka på **Redigera autentiseringsuppgifter** och ange autentiseringsuppgifter med åtkomst till Log Analytics arbets ytan i Azure Monitor. Om du behöver tvåfaktorautentisering väljer du **OAuth2** för **autentiseringsmetoden** för att uppmanas att logga in med dina autentiseringsuppgifter.

    ![Power BI schema](media/powerbi/powerbi-schedule.png)

5. Under **schemalagd uppdatering** aktiverar du alternativet för att **hålla dina data** aktuella.  Du kan också ändra **uppdaterings frekvensen** och en eller flera angivna tider för att köra uppdateringen.

    ![Power BI uppdatera](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggs ökningar](../logs/log-query-overview.md) för att bygga frågor som kan exporteras till Power BI.
* Lär dig mer om [Power BI](https://powerbi.microsoft.com) att bygga visualiseringar baserat på Azure Monitor logg exporter.