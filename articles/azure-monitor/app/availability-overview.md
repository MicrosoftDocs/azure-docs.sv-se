---
title: Application Insights tillgänglighetsöversikt
description: Konfigurera återkommande webbtester för att övervaka tillgänglighet och svarstider för din app eller webbplats.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 3c2d248df98fcb9f75f5de9c05b9f600955cbac7
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600802"
---
# <a name="availability-tests-overview"></a>Översikt över tillgänglighetstester

När du har distribuerat din webbapp/webbplats kan du konfigurera återkommande tester för att övervaka tillgänglighet och svarstider. [Application Insights](./app-insights-overview.md) skickar webbförfrågningar till ditt program med jämna mellanrum från platser över hela världen. Den kan meddela dig om programmet inte svarar eller om det svarar för långsamt.

Du kan konfigurera tillgänglighetstester för valfri HTTP- eller HTTPS-slutpunkt som kan nås från det offentliga Internet. Du behöver inte göra några ändringar på webbplatsen som du testar. I själva verket behöver det inte ens vara en webbplats som du äger. Du kan testa tillgängligheten för en REST API som tjänsten är beroende av.

## <a name="types-of-availability-tests"></a>Typer av tillgänglighetstester

Det finns tre typer av tillgänglighetstester:

* [URL-pingtest:](monitor-web-app-availability.md)Den här kategorin har två enkla tester som du kan skapa via portalen.
* [Webbtest med flera steg:](availability-multistep.md)En inspelning av en sekvens med webbförfrågningar som kan spelas upp för att testa mer komplexa scenarier. Webbtester i flera steg skapas i Visual Studio Enterprise och laddas upp till portalen för körning.
* [Anpassade tillgänglighetstester](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)för spåra: Om du väljer att skapa ett anpassat program för att köra tillgänglighetstester kan metoden användas för att skicka `TrackAvailability()` resultatet till Application Insights.

> [!IMPORTANT]
> Både [URL-pingtest och](monitor-web-app-availability.md) [webbtest med](availability-multistep.md) flera steg förlitar sig på den offentliga Internet-DNS-infrastrukturen för att matcha domännamnen för de testade slutpunkterna. Det innebär att om du använder Privat DNS måste du antingen se till att varje domännamn i testet också kan matchas av de offentliga domännamnsservrarna eller, om det inte är möjligt, använda anpassade tillgänglighetstester för spåra i [stället.](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)

**Du kan skapa upp till 100 tillgänglighetstester per Application Insights resurs.**

## <a name="troubleshooting"></a>Felsökning

Dedikerad [felsökningsartikel](troubleshoot-availability.md).

## <a name="next-step"></a>Nästa steg

* [Tillgänglighetsaviseringar](availability-alerts.md)
* [Webbtester i flera steg](availability-multistep.md)
* [URL-tester](monitor-web-app-availability.md)
* [Skapa och kör anpassade tillgänglighetstester med hjälp av Azure Functions.](availability-azure-functions.md)