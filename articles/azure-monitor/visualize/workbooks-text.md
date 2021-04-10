---
title: Text parametrar för Azure Monitor-arbetsböcker
description: Förenkla komplex rapportering med färdiga och anpassade arbets böcker med parametrar. Läs mer om text parametrar för arbets bok.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 22078f242ddeb882b39b85769537b9a282741250
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725534"
---
# <a name="workbook-text-parameters"></a>Text parametrar för arbets bok

Med text Rute parametrar får du ett enkelt sätt att samla in text från arbets boks användare. De används när det inte är praktiskt att använda en listruta för att samla in indatamängden (till exempel ett godtyckligt tröskelvärde eller allmänna filter). Med arbets böcker kan författare Hämta standardvärdet för text rutan från en fråga. Detta möjliggör intressanta scenarier som att ställa in standard tröskeln baserat på måttets P95.

En vanlig användning av text rutor är som interna variabler som används av andra arbets boks kontroller. Detta görs genom att använda en fråga för standardvärden och göra indatamängden osynlig i Read-mode. En användare kan till exempel vilja att ett tröskelvärde ska komma från en formel (inte en användare) och sedan använda tröskelvärdet i efterföljande frågor.

## <a name="creating-a-text-parameter"></a>Skapa en text parameter
1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `SlowRequestThreshold`
    2. Parameter typ: `Text`
    3. Kunna `checked`
    4. Hämta standardvärdet från fråga: `unchecked`
5. Skapa parametern genom att välja Spara i verktygsfältet.

    ![Bild som visar hur en text parameter skapas](./media/workbooks-text/text-create.png)

Så här kommer arbets boken att se ut i Read-mode.

![Bild som visar en text parameter i läsläge](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Referens till en text parameter
1. Lägg till en frågeplan i arbets boken genom att välja den blå `Add query` länken och välj en Application Insights resurs.
2. I rutan KQL lägger du till följande kodfragment:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Genom att använda text parametern med värdet 500 kopplat till den fråga som du använder för att köra frågan nedan:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Kör fråga för att visa resultaten

    ![Bild som visar en text parameter som refereras i KQL](./media/workbooks-text/text-reference.png)

> [!NOTE]
> I exemplet ovan `{SlowRequestThreshold}` representerar ett heltals värde. Om du frågar efter en sträng, t. ex. om `{ComputerName}` du skulle behöva ändra din Kusto-fråga för att lägga till offerter `"{ComputerName}"` för parameter fältet till ett accept-inmatat värde utan citat tecken.

## <a name="setting-default-values"></a>Ange standardvärden
1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `SlowRequestThreshold`
    2. Parameter typ: `Text`
    3. Kunna `checked`
    4. Hämta standardvärdet från fråga: `checked`
5. I rutan KQL lägger du till följande kodfragment:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Den här frågan anger standardvärdet för text rutan till 95 percentilen för alla förfrågningar i appen.
6. Kör fråga för att se resultatet
7. Skapa parametern genom att välja Spara i verktygsfältet.

    ![Bild som visar en text parameter med standardvärdet från KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Det här exemplet frågar Application Insights data, men metoden kan användas för alla loggbaserade data källor – Log Analytics, Azure Resource Graph osv.

## <a name="next-steps"></a>Nästa steg

* [Kom igång](./workbooks-overview.md#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](./workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.