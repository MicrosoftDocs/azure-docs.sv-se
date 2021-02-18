---
title: Azure Monitor arbets böcker skapa parametrar
description: Lär dig hur parametrar tillåter att arbets boks författare samlar in inmatade användare och refererar till dem i andra delar av arbets boken.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7c086938ec98fc1d34ba21f678b2091c3b5ac1c4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621592"
---
# <a name="workbook-parameters"></a>Arbets boks parametrar

Med parametrar kan arbets boks författare samla in inmatade uppgifter från användarna och referera till dem i andra delar av arbets boken – vanligt vis för att begränsa resultat uppsättningen eller ställa in rätt visuellt objekt. Det är en viktig funktion som gör det möjligt för författarna att bygga interaktiva rapporter och upplevelser. 

Med arbets böcker kan du styra hur parameter kontrollerna presenteras för konsumenter – text ruta vs. list rutor, Single-vs. Multi-SELECT, Values från text, JSON, KQL eller Azure Resource Graph osv.  

Parameter typer som stöds är:
* [Tid](workbooks-time.md) – tillåter att en användare väljer från förifyllda tidsintervall eller väljer ett anpassat intervall
* [Listruta](workbooks-dropdowns.md) – tillåter att en användare väljer från ett värde eller en uppsättning värden
* [Text](workbooks-text.md) – tillåter att en användare anger godtycklig text
* [Resurs](workbooks-resources.md) – låter en användare välja en eller flera Azure-resurser
* [Prenumeration](workbooks-resources.md) – tillåter att en användare väljer en eller flera Azure-prenumerations resurser
* Resurs typ – tillåter att en användare väljer en eller flera värden för resurs typ i Azure
* Plats – låter en användare välja ett eller flera värden för Azure-platser

Dessa parameter värden kan refereras till i andra delar av arbets böcker, antingen via bindningar eller värde expansionar.

## <a name="creating-a-parameter"></a>Skapa en parameter
1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `TimeRange` *(Observera att parameter __namn__ **får inte** innehålla blank steg eller specialtecken)*
    2. Visnings namn: `Time Range` *( __visnings namn__ kan dock innehålla blank steg, specialtecken, emoji osv.)*  
    2. Parameter typ: `Time range picker`
    3. Kunna `checked`
    4. Tillgängliga tidsintervall: senaste timmen, de senaste 12 timmarna, de senaste 24 timmarna, de senaste 48 timmarna, de senaste 3 dagarna, senaste 7 dagarna och alternativet Tillåt anpassat tidsintervall
5. Skapa parametern genom att välja Spara i verktygsfältet.

   ![Bild som visar skapandet av en tids intervalls parameter](./media/workbooks-parameters/time-settings.png)

Så här kommer arbets boken att se ut i Read-mode i formatet "Pills".

   ![Bild som visar en tids intervall parameter i läsläge](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Referera till en parameter
### <a name="via-bindings"></a>Via bindningar
1. Lägg till en frågeplan i arbets boken och välj en Application Insights resurs.
2. Öppna List rutan _tidsintervall_ och välj `Time Range` alternativet från avsnittet parametrar längst ned.
3. Detta binder tids intervall parametern till tidsintervallet för diagrammet. Tidsintervallet för exempel frågan är nu de senaste 24 timmarna.
4. Kör fråga för att visa resultaten

    ![Bild som visar en tidsintervalls parameter som refereras via bindningar](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>I KQL
1. Lägg till en frågeplan i arbets boken och välj en Application Insights resurs.
2. I KQL anger du ett tids omfattnings filter med hjälp av parametern: `| where timestamp {TimeRange}`
3. Detta utökar tiden för utvärdering av fråga till `| where timestamp > ago(1d)` , vilket är tids intervall svärdet för parametern.
4. Kör fråga för att visa resultaten

    ![Bild som visar ett tidsintervall som refereras i KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>I text 
1. Lägg till en text kontroll i arbets boken.
2. I markdown anger du `The chosen time range is {TimeRange:label}`
3. Välj _klar redigering_
4. Text kontrollen visar text: _det valda tidsintervallet är de senaste 24 timmarna_

## <a name="parameter-options"></a>Parameter alternativ
_I text_ -avsnittet som används `label` för parametern i stället för dess värde. Parametrarna visar olika alternativ beroende på dess typ, t. ex. tidsintervall som tillåter värde, etikett, fråga, start, slut och kornig het.

Använd `Previews` avsnittet i fönstret _Redigera parameter_ för att se expansions alternativen för din parameter:

![Bild som visar ett intervall parameter alternativ](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Nästa steg

* [Kom igång](../platform/workbooks-overview.md#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](../platform/workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.