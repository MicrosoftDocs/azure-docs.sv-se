---
title: Power BI utdata från Azure Stream Analytics
description: I den här artikeln beskrivs hur du matar ut data från Azure Stream Analytics till Power BI.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: cc48d26c217f52dfa52ac6cd3d7f18a806c93927
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740988"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Power BI utdata från Azure Stream Analytics

Du kan använda [Power BI](https://powerbi.microsoft.com/) som utdata för ett Stream Analytics jobb för att tillhandahålla en omfattande visualiserings upplevelse av analys resultaten. Du kan använda den här funktionen för instrument paneler för drift, rapportgenerering och måttbaserade rapporter.

Power BI utdata från Stream Analytics är för närvarande inte tillgängligt i regionerna Azure Kina 21Vianet och Azure Tyskland (T-Systems International).

## <a name="output-configuration"></a>Konfiguration av utdata

I följande tabell visas egenskaps namn och beskrivningar för att konfigurera Power BI-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ange ett eget namn som används i frågor för att dirigera frågeresultatet till Power BI utdata. |
| Grupp arbets yta |Om du vill aktivera delning av data med andra Power BI användare kan du välja grupper inuti ditt Power BI konto eller välja **min arbets yta** om du inte vill skriva till en grupp. Att uppdatera en befintlig grupp kräver att Power BI autentisering förnyas. |
| Namn på datauppsättning |Ange ett namn på data uppsättningen som du vill att Power BI-utdata ska använda. |
| Tabellnamn |Ange ett tabell namn under data uppsättningen för Power BI utdata. För närvarande kan Power BI utdata från Stream Analytics-jobb bara ha en tabell i en data uppsättning. |
| Godkänna anslutning | Du måste auktorisera med Power BI för att konfigurera dina inställningar för utdata. När du har beviljat dessa utdata åtkomst till din Power BI-instrumentpanel kan du återkalla åtkomsten genom att ändra användar kontots lösen ord, ta bort jobbets utdata eller ta bort Stream Analyticss jobbet. | 

En genom gång av hur du konfigurerar en Power BI utdata och en instrument panel finns i själv studie kursen [Azure Stream Analytics och Power BI](stream-analytics-power-bi-dashboard.md) .

> [!NOTE]
> Skapa inte uttryckligen data uppsättningen och tabellen i Power BI instrument panelen. Data uppsättningen och tabellen fylls i automatiskt när jobbet startas och jobbet börjar pumpa utdata till Power BI. Om jobb frågan inte genererar några resultat skapas inte data uppsättningen och tabellen. Om Power BI redan har en data uppsättning och en tabell med samma namn som det som angavs i det här Stream Analyticss jobbet, skrivs befintliga data över.
>

### <a name="create-a-schema"></a>Skapa ett schema

Azure Stream Analytics skapar en Power BI data uppsättning och ett tabell schema för användaren om de inte redan finns. I alla andra fall uppdateras tabellen med nya värden. För närvarande kan endast en tabell finnas i en data uppsättning. 

Power BI använder bevarande principen First-in, First-Out (FIFO). Data samlas in i en tabell tills den når 200 000 rader.

> [!NOTE]
> Vi rekommenderar inte att du använder flera utdata för att skriva till samma data uppsättning eftersom det kan orsaka flera problem. Varje utdata försöker skapa en Power BI data uppsättning oberoende av varandra, vilket kan resultera i flera data uppsättningar med samma namn. Om utdata inte har konsekventa scheman, ändrar data uppsättningen schemat vid varje skrivning, vilket leder till för många schema ändrings begär Anden. Även om de här problemen undviks, kommer flera utdata att vara mindre utförda än en och samma kopplade utdata.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konvertera en datatyp från Stream Analytics till Power BI

Azure Stream Analytics uppdaterar data modellen dynamiskt vid körning om schemat ändras. Kolumn namns ändringar, kolumn typ ändringar och tillägg eller borttagning av kolumner spåras.

Den här tabellen innehåller data typ konverteringar från [Stream Analytics data typer](/stream-analytics-query/data-types-azure-stream-analytics) till Power BI [typer av enhets data modeller (EDM)](/dotnet/framework/data/adonet/entity-data-model)om det inte finns någon Power BI data uppsättning och tabell.

Från Stream Analytics | För att Power BI
-----|-----
bigint | Int64
nvarchar(max) | Sträng
datetime | Datumtid
flyt | Double
Post mat ris | Sträng typ, konstant värde "IRecord" eller "IArray"

### <a name="update-the-schema"></a>Uppdatera schemat

Stream Analytics härleder data modell schema baserat på den första uppsättningen händelser i utdata. Senare, om det behövs, uppdateras data modellens schema för att hantera inkommande händelser som kanske inte passar in i det ursprungliga schemat.

Undvik `SELECT *` frågan för att förhindra dynamisk schema uppdatering på rader. Förutom potentiella prestanda konsekvenser kan det leda till osäkerhet vid den tid det tar för resultaten. Välj de exakta fält som ska visas på instrument panelen för Power BI. Dessutom bör datavärdena vara kompatibla med den valda data typen.

Föregående/aktuell | Int64 | Sträng | Datumtid | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sträng | Sträng | Double
Double | Double | Sträng | Sträng | Double
Sträng | Sträng | Sträng | Sträng | Sträng 
Datumtid | Sträng | Sträng |  Datumtid | Sträng

## <a name="output-batch-size"></a>Batchstorlek för utdata

För batch-storlek för utdata, se [Power BI REST API-gränser](/power-bi/developer/automation/api-rest-api-limitations).

## <a name="next-steps"></a>Nästa steg

* [Använd hanterad identitet för att autentisera ditt Azure Stream Analytics jobb till Power BI (förhands granskning)](powerbi-output-managed-identity.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)