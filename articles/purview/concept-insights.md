---
title: Förstå Insights-rapporter i Azure avdelningens kontroll
description: I den här artikeln förklaras vilka insikter som finns i Azure avdelningens kontroll.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96554763"
---
# <a name="understand-insights-in-azure-purview"></a>Förstå insikter i Azure Purview

Den här artikeln innehåller en översikt över insikts funktionen i Azure avdelningens kontroll.

Insikter är en av nyckel pelarna i avdelningens kontroll. Funktionen förser kunder, en ruta med glas visning i sin katalog och ger ytterligare till gång till specifika insikter om administratörer för data källor, företags användare, data chefer, data befäl och säkerhets administratörer. För närvarande har avdelningens kontroll följande insikts rapporter som kommer att vara tillgängliga för kunder i den offentliga för hands versionen.

## <a name="asset-insights"></a>Till gångs insikter

Den här rapporten ger en översikt över din datafastighets ögon och dess distribution efter källtyp, efter klassificering och efter fil storlek som några av dimensionerna. Den här rapporten följer olika typer av användare som kan hantera avdelningens kontroll-kontot och köra genomsökningar eller företags användare som kan vara intresserade av att veta hur många till gångar som finns med en viss klassificering inom organisationens data fastighets. 

Rapporten ger bred insikter genom grafer och KPI: er och senare insikter om vissa avvikelser, till exempel felplacerade filer. Rapporten har även stöd för en komplett kund upplevelse där kunden kan se antalet till gångar med en specifik klassificering, kan dela upp informationen efter käll typer och mappar, och kan också visa en lista över till gångar för ytterligare undersökning.

## <a name="scan-insights"></a>Skanna insikter

Rapporten gör det möjligt för administratörer att förstå övergripande hälso tillstånd för genomsökningar – hur många som lyckas, hur många som har misslyckats, hur många som har avbrutits. Den här rapporten ger en status uppdatering för genomsökningar som har körts i avdelningens kontroll-kontot inom en tids period under de senaste sju dagarna eller de senaste 30 dagarna.

Rapporten gör det också möjligt för administratörer att ta del av och utforska vilka genomsökningar som misslyckats och på vilka olika typer av källor. Om du vill göra det lättare för användare att undersöka rapporten hjälper de till att navigera till sidan med genomsöknings historik i "källor".

## <a name="glossary-insights"></a>Ord lista

Den här rapporten ger företags användare och data bevarar en status rapport på ord lista. Användare kan visa den här rapporten för att förstå distribution av ord lista efter status, se hur många ord som är kopplade till till gångar och hur många som ännu inte är kopplade till någon till gång. Företags användare kan också lära sig om hur många ord som är färdiga. 

Den här rapporten sammanfattar de viktigaste objekten som en affärs användare eller data ledning behöver fokusera på, för att skapa en komplett och användbar ord lista för sin organisation. Användarna kan också navigera till "ord lista"-upplevelsen från "ord" i "ord för insikter" för att göra ändringar på en speciell ord lista.

## <a name="classification-insights"></a>Klassificerings insikter

Den här rapporten innehåller information om var klassificerade data finns, vilka klassificeringar som påträffas vid en genomsökning och en detaljerad information till själva klassificerade filer. Det gör det möjligt för säkerhets administratörer att förstå vilka typer av information som finns i organisationens data fastighets. 

I Azure avdelningens kontroll liknar klassificeringarna ämnes koder och används för att markera och identifiera innehåll av en speciell typ i din datafastighet.

Använd rapporten klassificerings insikter för att identifiera innehåll med vissa klassificeringar och förstå nödvändiga åtgärder, till exempel att lägga till ytterligare säkerhet i databaserna eller flytta innehåll till en säkrare plats.

Mer information finns i [klassificera insikter om dina data från Azure avdelningens kontroll](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Känslighet för att märka insikter

Den här rapporten innehåller information om känslighets etiketter som påträffas under en genomsökning, samt en detaljerad lista över de märkta filerna. Det gör det möjligt för säkerhets administratörer att se till att information som finns i organisationens datafastighet är säker. 

I Azure avdelningens kontroll används känslighets etiketter för att identifiera klassificerings typ kategorier, samt de grup säkerhets principer som du vill använda för varje kategori.

Använd rapporten etikettera insikter för att identifiera de känslighets etiketter som finns i ditt innehåll och förstå nödvändiga åtgärder, till exempel hantering av åtkomst till vissa databaser eller filer.

Mer information finns i [känslighets etiketter om dina data i Azure avdelningens kontroll](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Fil namns insikter

Den här rapporten innehåller information om fil namns tilläggen eller filtyper som påträffas vid en genomsökning, samt en detaljerad information till filerna. 

Använd filen med fil namns insikter för att förstå hur många filer varje tid du har, var dessa filer finns och om de är lättlästa för känsliga data.

Mer information finns i avsnittet [om fil namns tillägg för dina data från Azure avdelningens kontroll](file-extension-insights.md).

## <a name="next-steps"></a>Nästa steg

* [Ord lista](glossary-insights.md)
* [Skanna insikter](scan-insights.md)
* [Klassificerings insikter](./classification-insights.md)