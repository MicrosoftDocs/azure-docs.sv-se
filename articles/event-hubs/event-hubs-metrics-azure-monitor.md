---
title: Mått i Azure Monitor Azure-Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Azure Monitor för att övervaka Azure Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 74830775a4f31e6f8e486b4d6cc434335b4ee723
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165900"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Event Hubs-mått i Azure Monitor

Event Hubs mått ger dig tillstånd att Event Hubs resurser i din Azure-prenumeration. Med en omfattande uppsättning Mät data kan du utvärdera den övergripande hälsan för dina händelse nav inte bara på namn områdes nivån, utan även på enhets nivå. Den här statistiken kan vara viktig när de hjälper dig att övervaka status för dina Event Hub. Mått kan också hjälpa till att felsöka rotor Saks problem utan att behöva kontakta Azure-supporten.

Azure Monitor tillhandahåller enhetliga användar gränssnitt för övervakning i olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../azure-monitor/overview.md) och [Hämta Azure Monitor mått med .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -exempel på GitHub.

## <a name="access-metrics"></a>Åtkomst mått

Azure Monitor ger till gång till mått på flera sätt. Du kan antingen komma åt mått via [Azure Portal](https://portal.azure.com)eller använda Azure Monitor-API: er (rest och .net) och analys lösningar som Log Analytics och Event Hubs. Mer information finns i [övervaka data som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna med data. Om du behöver lagra data under en längre tids period kan du arkivera mått data till ett Azure Storage konto. Den här inställningen kan konfigureras i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Åtkomst mått i portalen

Du kan övervaka mått över tid i [Azure Portal](https://portal.azure.com). I följande exempel visas hur du visar lyckade förfrågningar och inkommande begär Anden på konto nivå:

![Visa lyckade mått][1]

Du kan också komma åt mått direkt via namn området. Det gör du genom att markera ditt namn område och sedan välja **mått**. Om du vill visa mått som filtrerats till händelse hubbens omfång väljer du händelsehubben och sedan **mått**.

För mått som stöder dimensioner måste du filtrera med det önskade dimension svärdet som visas i följande exempel:

![Filtrera med dimensions värde][2]

## <a name="billing"></a>Fakturering

Användning av mått i Azure Monitor är för närvarande kostnads fritt. Men om du använder andra lösningar som inhämtar mått data kan du debiteras av dessa lösningar. Till exempel debiteras du per Azure Storage om du arkiverar mått data till ett Azure Storage konto. Du debiteras också av Azure om du strömmar mått data till Azure Monitor loggar för avancerad analys.

Följande mått ger en översikt över hälso tillståndet för din tjänst. 

> [!NOTE]
> Vi har föråldrat flera mått när de flyttas under ett annat namn. Detta kan kräva att du uppdaterar dina referenser. Mått som har marker ATS med nyckelordet "inaktuell" kommer inte att stödjas framåt.

Alla mått värden skickas till Azure Monitor varje minut. Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Det tidsintervall som stöds för alla Event Hubs mått är 1 minut.

## <a name="azure-event-hubs-metrics"></a>Azure Event Hubs mått
En lista över mått som stöds av tjänsten finns i [Azure Event Hubs](../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)

> [!NOTE]
> När ett användar fel inträffar uppdaterar Azure Event Hubs måttet för **användar fel** , men loggar inte någon annan diagnostikinformation. Därför måste du samla in information om användar fel i dina program. Eller också kan du konvertera den telemetri som genereras när meddelanden skickas eller tas emot i Application Insights. Ett exempel finns i [Spåra med Application Insights](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights).

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure Monitor integrering med SIEM-verktyg
Routning av övervaknings data (aktivitets loggar, diagnostikloggar och så vidare) till en Event Hub med Azure Monitor gör att du enkelt kan integrera med verktyg för säkerhets informations-och händelse hantering (SIEM). Mer information finns i följande artiklar/blogg inlägg:

- [Strömma Azure övervaknings data till en Event Hub för användning av ett externt verktyg](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Introduktion till Azure Log Integration](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

I scenariot där ett SIEM-verktyg använder loggdata från en Event Hub, om du inte ser några inkommande meddelanden eller om du ser inkommande meddelanden, men inte har några utgående meddelanden i mått diagrammet, följer du dessa steg:

- Om det inte finns **några inkommande meddelanden** innebär det att tjänsten Azure Monitor inte flyttar gransknings-/diagnostikloggar till händelsehubben. Öppna ett support ärende med Azure Monitor-teamet i det här scenariot. 
- om det finns inkommande meddelanden, men **inga utgående meddelanden**, innebär det att Siem-programmet inte läser meddelandena. Kontakta SIEM-providern för att avgöra om konfigurationen av Event Hub som programmen är korrekt.


## <a name="next-steps"></a>Nästa steg

* Se [Översikt över Azure Monitoring](../azure-monitor/overview.md).
* [Hämta Azure Monitor mått med .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -exempel på GitHub. 

Besök följande länkar för mer utförlig information om Event Hubs:

- Kom igång med en kurs om händelsehubbar
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
