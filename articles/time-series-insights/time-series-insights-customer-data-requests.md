---
title: Funktioner för begäran om kund data – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om kund data förfrågnings funktioner i Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b5ecbabe4a5c3458c77d145fbce1281550470e00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019080"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av funktioner för begäranden om kunddata

Azure Time Series Insights är en hanterad moln tjänst med komponenter för lagring, analys och visualisering som gör det enkelt att mata in, lagra, utforska och analysera miljarder händelser samtidigt.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Om du vill visa, exportera och ta bort personliga data som kan vara föremål för en begäran om data subjekt kan en Azure Time Series Insights innehavaradministratör använda antingen Azure Portal eller REST-API: erna. Om du använder tjänsten Azure Portal för att tillhandahålla tjänst data kan du använda en mindre komplex metod för att utföra dessa åtgärder som de flesta användare föredrar.

## <a name="identifying-customer-data"></a>Identifiera kund information

Azure Time Series Insights anser att person uppgifter är data kopplade till administratörer och användare av Time Series Insights. Time Series Insights lagrar Azure Active Directory objekt-ID för användare med åtkomst till miljön. Azure Portal visar användarens e-postadresser, men de här e-postadresserna lagras inte i Time Series Insights, de söks dynamiskt med Azure Active Directory objekt-ID i Azure Active Directory.

## <a name="deleting-customer-data"></a>Tar bort kund information

En klient organisations administratör kan ta bort kund information med hjälp av Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Men innan du tar bort kunddata via portalen bör du ta bort användarens åtkomst principer från Time Series Insights miljön i Azure Portal. Mer information finns i avsnittet [bevilja åtkomst till en Time Series Insights miljö med hjälp av Azure Portal](./concepts-access-policies.md).

Du kan också utföra borttagnings åtgärder på åtkomst principer med hjälp av REST API. Mer information finns i avsnittet om [åtkomst principer – ta bort](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/delete).

Time Series Insights är integrerat med princip bladet i Azure Portal. Både Time Series Insights och princip bladet gör att du kan visa, exportera och ta bort användar data som lagras i tjänsten. Eventuella borttagnings åtgärder som vidtas på bladet princip i Azure Portal resulterar i att användar data tas bort i Time Series Insights. Om en användare till exempel har sparat en personlig fråga, tas frågan bort permanent från Time Series Insights Explorer. Om användaren har en sparad delad fråga kvarstår frågan, men användar informationen tas bort permanent. Följande kommentar innehåller instruktioner om hur du utför dessa uppgifter.

## <a name="exporting-customer-data"></a>Exportera kund information

På samma sätt som för att ta bort data kan en innehavaradministratör Visa och exportera data som lagras i Time Series Insights från bladet princip i Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Om du är klient organisations administratör kan du visa data åtkomst principer i Time Series Insightss miljön i Azure Portal. Mer information finns i avsnittet [bevilja åtkomst till en Time Series Insights miljö med hjälp av Azure Portal](./concepts-access-policies.md).

Du kan också utföra export åtgärder på åtkomst principer med hjälp av åtgärden "lista efter miljö" i den angivna REST API. Mer information finns i avsnittet om [åtkomst principer – lista efter miljö](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Ta bort data som lagras i Time Series Insights

Person uppgifter kan göra sitt sätt att Time Series Insights lagring, ett annat scenario från användar-och administratörs data. Om du anser att data som lagras i Time Series Insights som personliga data kan du exportera och ta bort dessa data med hjälp av följande steg:

### <a name="view-and-export-data"></a>Visa och exportera data

Om du vill visa och exportera data som lagras i Time Series Insights måste du söka efter dessa data. Du kan använda Time Series Insights Explorer eller Time Series Insights fråge-API: er för att visa och exportera data. Om du vill visa och exportera data med hjälp av Time Series Insights Explorer börjar du med att söka efter användar data i fråga. Efter sökningen högerklickar du på diagrammet och väljer **utforska händelser**. Events-rutnätet visas och visar alternativ för att exportera data som CSV och JSON.

Mer information finns i [Azure Time Series Insights Explorer](time-series-insights-explorer.md).

### <a name="delete-data"></a>Ta bort data

För närvarande stöder Time Series Insights inte detaljerad borttagning av data. Time Series Insights ger dock möjlighet att ta bort kund information som lagras i Time Series Insights genom att konfigurera bevarande principer. Du kan justera Retentions perioden för hela Time Series Insightss miljön till valfritt antal dagar för att stödja dina borttagnings krav.

Mer information finns [i Konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att bevilja data åtkomst till din Azure Time Series Insightss miljö](./concepts-access-policies.md).

* Visa [Azure Time Series Insights Explorer](time-series-insights-explorer.md).

* Läs mer om att [Konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).