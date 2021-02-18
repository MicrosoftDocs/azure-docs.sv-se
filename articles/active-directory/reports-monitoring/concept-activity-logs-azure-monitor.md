---
title: Azure Active Directory aktivitets loggar i Azure Monitor | Microsoft Docs
description: Introduktion till Azure Active Directory aktivitets loggar i Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73130c906d4d9f0da51db1b666e8562570cce40f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571266"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure AD-aktivitetsloggar i Azure Monitor

Du kan dirigera Azure Active Directory (Azure AD)-aktivitets loggar till flera slut punkter för långsiktig kvarhållning och data insikter. Med den här funktionen kan du:

* Arkivera Azure AD-aktivitets loggar till ett Azure Storage-konto för att bevara data under en längre tid.
* Strömma Azure AD-aktivitets loggar till en Azure Event Hub för analys med hjälp av populära SIEM-verktyg (Security information and Event Management), till exempel Splunk och QRadar.
* Integrera Azure AD-aktivitets loggar med dina egna anpassade logg lösningar genom att strömma dem till en Event Hub.
* Skicka Azure AD-aktivitets loggar till Azure Monitor loggar för att aktivera omfattande visualiseringar, övervakning och avisering på anslutna data.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Rapporter som stöds

Du kan skicka Azure AD audit-loggar och inloggnings loggar till ditt Azure Storage-konto, händelsehubben, Azure Monitor loggar eller anpassad lösning med hjälp av den här funktionen. 

* **Spårningsloggar**: [Rapporten om spårningsloggaktivitet](concept-audit-logs.md) ger dig åtkomst till historiken för varje uppgift som utförs i din klientorganisation.
* **Inloggningsloggar**: Med [rapporten om inloggningsaktivitet](concept-sign-ins.md) kan du se vem som har utfört de uppgifter som rapporteras i spårningsloggarna.

> [!NOTE]
> B2C-relaterade spårnings- och inloggningsaktivitetsloggar stöds inte just nu.
>

## <a name="prerequisites"></a>Krav

För att använda funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* [Licens](https://azure.microsoft.com/pricing/details/active-directory/) för Azure AD Free, Basic, Premium 1 eller Premium 2 för åtkomst till Azure AD-granskningsloggar på Azure Portal. 
* En Azure AD-klientorganisation.
* En användare som är **global administratör** eller **säkerhetsadministratör** för Azure AD-klientorganisationen.
* [Licens](https://azure.microsoft.com/pricing/details/active-directory/) för Azure AD Premium 1 eller Premium 2 för åtkomst till Azure AD-inloggningsloggar på Azure Portal. 

Beroende på vart du vill dirigera spårningsloggdata behöver du något av följande:

* Ett Azure-lagringskonto som du har *ListKeys*-behörigheter för. Vi rekommenderar att du använder ett allmänt lagringskonto och inte ett blob-lagringskonto. Information om lagringspriser hittar du i [Priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* En Azure Event Hubs-namnrymd för att integrera med lösningar från tredje part.
* En Azure Log Analytics-arbetsyta för att skicka loggar till Azure Monitor loggar.

## <a name="cost-considerations"></a>Kostnadsöverväganden

Om du redan har en Azure AD-licens behöver du en Azure-prenumeration för att konfigurera lagringskontot och händelsehubben. Azure-prenumerationen är kostnadsfri, men du behöver betala för att använda Azure-resurser, inklusive det lagringskonto som du använder för arkivering och den händelsehubb som du använder för strömning. Mängden data och därmed kostnaden kan variera avsevärt beroende på storleken på klientorganisationen. 

### <a name="storage-size-for-activity-logs"></a>Lagringsstorlek för aktivitetsloggar

Varje granskningslogghändelse använder cirka 2 KB datalagring. Logga in händelse loggar är cirka 4 KB data lagring. I en klientorganisation med 100 000 användare skulle det ske ungefär 1,5 miljoner händelser per dag, vilket skulle kräva ungefär 3 GB datalagring per dag. Eftersom skrivningar sker i batchar om cirka fem minuter kan du förvänta dig ungefär 9 000 skrivåtgärder per månad. 


Följande tabell innehåller en uppskattning av kostnaden, beroende på klientorganisationens storlek, för ett GPv2-lagringskonto i USA, västra för minst ett års kvarhållning. För att skapa en mer tillförlitlig uppskattning av den datavolym som du förväntar dig att du behöver för programmet använder du [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).


| Loggkategori | Antal användare | Händelser per dag | Datavolym per månad (uppskattad) | Kostnad per månad (uppskattad) | Kostnad per år (uppskattad) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Granska | 100 000 | 1,5&nbsp;miljoner | 90 GB | 1,93 USD | 23,12 USD |
| Granska | 1 000 | 15 000 | 900 MB | 0,02 USD | 0,24 USD |
| Inloggningar | 1 000 | 34 800 | 4 GB | 0,13 USD | 1,56 USD |
| Inloggningar | 100 000 | 15&nbsp;miljoner | 1,7 TB | 35,41 USD | 424,92 USD |
 









### <a name="event-hub-messages-for-activity-logs"></a>Händelsehubbmeddelanden för aktivitetsloggar

Händelser batchas i intervall på ungefär fem minuter och skickas som ett enskilt meddelande som innehåller alla händelser inom den tidsramen. Ett meddelande i en händelsehubb har en maximal storlek på 256 KB, och om den totala storleken på alla meddelanden inom tidsramen överstiger den volymen skickas flera meddelanden. 

Exempelvis inträffar normalt cirka 18 händelser per sekund för en stor klientorganisation med fler än 100 000 användare, vilket är en hastighet som motsvarar 5 400 händelser var femte minut. Eftersom spårningsloggar är cirka 2 KB per händelse motsvarar detta 10,8 MB data. Därför skickas 43 meddelanden till händelsehubben i det femminutsintervallet. 

Följande tabell innehåller uppskattade kostnader per månad för en grundläggande Event Hub i västra USA, beroende på volymen av händelse data som kan variera från klient organisation till klient organisation enligt många faktorer som användar inloggnings beteende osv. Använd [pris kalkylatorn Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)för att beräkna en exakt uppskattning av den data volym som du förväntar dig för ditt program.

| Loggkategori | Antal användare | Händelser per sekund | Händelser per femminutsintervall | Volym per intervall | Meddelanden per intervall | Meddelanden per månad | Kostnad per månad (uppskattad) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Granska | 100 000 | 18 | 5 400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Granska | 1 000 | 0.1 | 52 | 104 KB | 1 | 8 640 | 10,80 USD |
| Inloggningar | 100 000 | 18000 | 5 400 000 | 10,8 GB | 42188 | 364 504 320 | $23,9 |  
| Inloggningar | 1 000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 USD |  

### <a name="azure-monitor-logs-cost-considerations"></a>Azure Monitor loggar kostnads överväganden



| Loggkategori | Antal användare | Händelser per dag | Händelser per månad (30 dagar) | Kostnad per månad i USD (EST.) |
|:-|--|--|--|-:|
| Gransknings-och inloggnings program | 100 000 | 16 500 000 | 495 000 000 | $1093,00 |
| Granska | 100 000 | 1 500 000 | 45 000 000 | $246,66 |
| Inloggningar | 100 000 | 15 000 000 | 450 000 000 | $847,28 |










Om du vill granska kostnader som rör hantering av Azure Monitor loggar, se [hantera kostnader genom att kontrol lera data volymer och kvarhållning i Azure Monitor loggar](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Det här avsnittet innehåller svar på vanliga frågor och beskriver kända problem med Azure AD-loggar i Azure Monitor.

**F: Vilka loggar ingår?**

**S:** Både inloggningsaktivitetsloggarna och spårningsloggarna kan dirigeras via den här funktionen, men B2C-relaterade spårningsloggar ingår inte för närvarande. Om du vill ta reda på vilka typer av loggar och vilka funktionsbaserade loggar som stöds för närvarande kan du läsa [schemat för spårningslogg](reference-azure-monitor-audit-log-schema.md) och [schemat för inloggningslogg](reference-azure-monitor-sign-ins-log-schema.md). 

---

**F: hur snart efter en åtgärd visas motsvarande loggar i min Event Hub?**

**S**: Loggarna bör visas i din händelsehubb inom två till fem minuter efter att åtgärden har utförts. Mer information om Event Hubs finns i [Vad är Azure Event Hubs?](../../event-hubs/event-hubs-about.md).

---

**F: hur snart efter en åtgärd visas motsvarande loggar i mitt lagrings konto?**

**S:** För Azure-lagringskonton är svarstiden mellan 5 och 15 minuter efter att en åtgärd har genomförts.

---

**F: Vad händer om en administratör ändrar Retentions perioden för en diagnostisk inställning?**

**A**: den nya bevarande principen tillämpas på loggar som samlats in efter ändringen. Loggar som samlas in innan princip ändringen påverkas.

---

**F: Hur mycket kostar det att lagra mina data?**

**S:** Lagringskostnaden beror både på storleken på dina loggar och den kvarhållningsperiod som du väljer. En lista över de uppskattade kostnaderna för klientorganisationer, vilket beror på mängden loggar som genereras, finns i avsnittet [Lagringsstorlek för aktivitetsloggar](#storage-size-for-activity-logs).

---

**F: Hur mycket kostar det att strömma mina data till en händelsehubb?**

**S:** Kostnaden för strömning beror på hur många meddelanden du får per minut. Den här artikeln beskriver hur kostnaderna beräknas och visar kostnadsberäkningar som baseras på antalet meddelanden. 

---

**F: Kan jag integrera Azure AD-aktivitetsloggar med mitt SIEM-system?**

**S**: Du kan göra detta på två sätt:

- Använd Azure Monitor med Event Hubs för att streama loggar till ditt SIEM-system. [Strömma loggarna till en händelsehubb](tutorial-azure-monitor-stream-logs-to-event-hub.md) och [konfigurera sedan SIEM-verktyget](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) med den konfigurerade händelsehubben. 

- Använd [Reporting Graph API](concept-reporting-api.md) för att komma åt data och skicka dem till SIEM-systemet med hjälp av dina egna skript.

---

**F: Vilka SIEM-verktyg stöds för närvarande?** 

**A**: **a**: Azure Monitor stöds för närvarande av [Splunk](./howto-integrate-activity-logs-with-splunk.md), IBM QRadar, [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory), [ArcSight](./howto-integrate-activity-logs-with-arcsight.md), LogRhythm och Logz.io. Mer information om hur anslutningsapparna fungerar finns på sidan om att [strömma Azure-övervakningsdata till en händelsehubb för användning av ett externt verktyg](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

---

**F: Hur integrerar jag Azure AD-aktivitetsloggar med min Splunk-instans?**

**S**: [Dirigera Azure AD-aktivitetsloggarna till en händelsehubb](./tutorial-azure-monitor-stream-logs-to-event-hub.md) och följ sedan stegen för att [integrera aktivitetsloggar med Splunk](./howto-integrate-activity-logs-with-splunk.md).

---

**F: Hur integrerar jag Azure AD-aktivitetsloggar med Sumo Logic?** 

**S**: [Dirigera Azure AD-aktivitetsloggarna till en händelsehubb](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory) och följ sedan stegen för att [installera Azure AD-programmet och visa instrumentpaneler i SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**F: Kan jag komma åt data från en händelsehubb utan att använda ett externt SIEM-verktyg?** 

**A**: Ja. Om du vill komma åt loggarna från ditt anpassade program kan du använda [Event Hub API](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md). 

---


## <a name="next-steps"></a>Nästa steg

* [Arkivera aktivitetsloggar till ett lagringskonto](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Dirigera aktivitetsloggar till en händelsehubb](./tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Integrera aktivitets loggar med Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)