---
title: Övervaka hälsan för App Service instanser
description: Lär dig hur du övervakar hälso tillståndet för App Service instanser med hjälp av hälso kontroll.
keywords: Azure App Service, webbapp, hälso kontroll, väg trafik, felfria instanser, sökväg, övervakning,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 60a210c6c336c1b820015304e8ab53bc894c17bf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792509"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Övervaka App Service instanser med hjälp av hälso kontroll

![Hälso kontroll fel][2]

I den här artikeln används hälso kontroll i Azure Portal för att övervaka App Service instanser. Hälso kontroll ökar programmets tillgänglighet genom att ta bort felaktiga instanser. Din [App Service plan](/overview-hosting-plans) ska skalas till två eller fler instanser för att kunna använda hälso kontrollen. Hälso kontroll Sök vägen bör kontrol lera viktiga komponenter i ditt program. Om ditt program till exempel är beroende av en databas och ett meddelande system bör hälso kontrollens slut punkt ansluta till dessa komponenter. Om programmet inte kan ansluta till en kritisk komponent, ska sökvägen returnera en svars kod på 500 nivå för att indikera att appen inte är felfri.

## <a name="what-app-service-does-with-health-checks"></a>Vad App Service gör med hälso kontroller

- När du får en sökväg i appen pingar hälso kontrollen den här sökvägen på alla instanser av din App Service-app med intervall på 1 minut.
- Om en instans inte svarar med en status kod mellan 200-299 (inklusive) efter två eller fler begär Anden, eller om det inte går att svara på ping, avgör systemet om det är ohälsosamt och tar bort det.
- Efter borttagningen fortsätter hälso kontrollen att pinga den felaktiga instansen. Om den fortsätter att svara utan problem kan App Service starta om den underliggande virtuella datorn i ett arbete för att returnera instansen till felfritt tillstånd.
- Om en instans inte är felfri i en timme ersätts den med en ny instans.
- När du skalar upp eller ut, App Service pingar dessutom hälso kontroll Sök vägen för att säkerställa att nya instanser är klara.

> [!NOTE]
> Hälso kontrollen följer inte 302 omdirigeringar. Högst en instans kommer att ersättas per timme, med högst tre instanser per dag per App Service plan.
>

## <a name="enable-health-check"></a>Aktivera hälso kontroll

![Navigering för hälso kontroll i Azure Portal][3]

- Om du vill aktivera hälso kontroll bläddrar du till Azure Portal och väljer App Service-appen.
- Under **övervakning** väljer du **hälso kontroll**.
- Välj **Aktivera** och ange en giltig URL-sökväg till programmet, till exempel `/health` eller `/api/health` .
- Klicka på **Spara**.

> [!CAUTION]
> Konfigurations ändringar för hälso kontroll starta om appen. Vi rekommenderar att du [konfigurerar mellanlagrings platser](deploy-staging-slots.md) och växlar till produktion för att minimera påverkan på produktions program.
>

### <a name="configuration"></a>Konfiguration

Förutom att konfigurera hälso kontroll alternativen kan du också konfigurera följande [Inställningar för appen](configure-common.md):

| Namn på App-inställning | Tillåtna värden | Beskrivning |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Maximalt antal ping-försök. Om till exempel är inställt på `2` , tas instanserna bort efter `2` misslyckade ping-signaler. När du skalar upp eller ut, kommer App Service pinga till hälso kontroll Sök vägen för att se till att nya instanser är klara. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | För att undvika överbelastande av felfria instanser kommer högst hälften av instanserna att undantas. Till exempel, om en App Service plan skalas till fyra instanser och tre inte är felfria, kommer högst två att undantas. De andra två instanserna (en felfri och en skadad) fortsätter att ta emot begär Anden. I värsta fall där alla instanser är felaktiga kommer ingen att undantas. Om du vill åsidosätta det här beteendet ställer du in app-inställningen på ett värde mellan `0` och `100` . Ett högre värde innebär att fler felaktiga instanser tas bort (standard är 50). |

#### <a name="authentication-and-security"></a>Autentisering och säkerhet

Hälso kontrollen integreras med App Service funktioner för autentisering och auktorisering. Inga ytterligare inställningar krävs om de här säkerhetsfunktionerna är aktiverade. Om du använder ditt eget autentiseringspaket måste dock hälso kontroll Sök vägen tillåta anonym åtkomst. Om platsen bara är HTTP **s**– aktive rad skickas begäran om hälso kontroll via http **s**.

Stora företags utvecklings team behöver ofta följa säkerhets kraven för exponerade API: er. Om du vill skydda hälso kontrollens slut punkt bör du först använda funktioner som [IP-begränsningar](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [klient certifikat](app-service-ip-restrictions.md#set-an-ip-address-based-rule)eller en Virtual Network för att begränsa åtkomsten till programmet. Du kan skydda hälso kontroll slut punkten genom att kräva att `User-Agent` den inkommande begäran matchar `ReadyForRequest/1.0` . User-Agent kan inte manipuleras eftersom begäran redan skyddas av tidigare säkerhetsfunktioner.

## <a name="monitoring"></a>Övervakning

När du har angett din program hälso kontroll Sök väg kan du övervaka webbplatsens hälso tillstånd med hjälp av Azure Monitor. Från **hälso kontroll** bladet i portalen klickar du på **måtten** i det övre verktygsfältet. Då öppnas ett nytt blad där du kan se platsens historiska hälso status och skapa en ny varnings regel. Mer information om övervakning av dina platser [finns i hand boken för Azure Monitor](web-sites-monitor.md).

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitets logg avisering för att övervaka alla åtgärder för autoskalning av motorn i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitets logg avisering för att övervaka alla misslyckade skalnings-i/skala ut-åtgärder i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
