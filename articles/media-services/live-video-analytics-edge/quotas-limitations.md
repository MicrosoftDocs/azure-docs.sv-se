---
title: Video analys i real tid med IoT Edge kvoter och begränsningar – Azure
description: I den här artikeln beskrivs video analys i real tid för IoT Edge kvoter och begränsningar.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400532"
---
# <a name="quotas-and-limitations"></a>Kvoter och begränsningar

Den här artikeln räknar upp kvoter och begränsningar för live video analys i IoT Edge modul.

## <a name="maximum-period-of-disconnected-use"></a>Maximal period för frånkopplad användning

Edge-modulen kan upprätthålla en tillfällig förlust av Internet anslutning. Om modulen är frånkopplad i mer än 36 timmar inaktive ras alla diagram instanser som kördes. Alla ytterligare direkta metod anrop kommer att blockeras.

Om du vill återuppta modulen Edge till ett användnings tillstånd måste du återställa Internet anslutningen så att modulen kan kommunicera med Azure Media Service-kontot.

## <a name="maximum-number-of-graph-instances"></a>Maximalt antal diagram instanser

Högst 1000 diagram instanser per modul (som skapats via GraphInstanceSet) stöds.

## <a name="maximum-number-of-graph-topologies"></a>Maximalt antal diagram topologier

Högst 50 diagram-topologier per modul (som skapats via GraphTopologySet) stöds.

## <a name="limitations-on-graph-topologies-at-preview"></a>Begränsningar för graf-topologier i för hands versionen

I för hands versionen finns det begränsningar för olika noder som kan anslutas tillsammans i en Media Graph-topologi.

* RTSP-källa
   * Endast en RTSP-källa tillåts per graf-topologi.
* Processor för rörelse identifiering
   * Måste omedelbart underordnas från RTSP-källan.
   * Kan inte användas för en HTTP-eller gRPC-förlängning.
* Signal grind processor
   * Måste omedelbart underordnas från RTSP-källan.
* Till gångs mottagare 
   * Måste omedelbart underordnas från RTSP-källan eller signal grind processor.
* Fil mottagare
   * Måste omedelbart underordnas från signal grind processor.
   * Får inte omedelbart underordnas en HTTP-eller gRPC-förlängning av processor eller rörelse identifiering
* IoT Hub mottagare
   * Får inte vara direkt underordnad en IoT Hub källa.

## <a name="limitations-on-media-service-operations-at-preview"></a>Begränsningar för media service-åtgärder i för hands versionen

I samband med för hands versionen stöder Live Video Analytics på IoT Edge inte följande:

* Möjlighet att migrera Media Service-kontot från en prenumeration till en annan utan avbrott.
* Möjligheten att använda mer än ett lagrings konto med Media Service-kontot.
* Möjligheten att ändra tjänstens huvud namns information i önskade egenskaper för modulen dynamiskt, utan att starta om.

Du kan bara använda IP-kameror som stöder RTSP-protokollet. Du hittar IP-kameror som har stöd för RTSP på sidan [ONVIF-produkter](https://www.onvif.org/conformant-products) . Sök efter enheter som uppfyller profilerna G, S eller T.

Ytterligare bör du konfigurera dessa kameror att använda H. 264 video och AAC-ljud. Andra codecenheter stöds inte för närvarande. 

## <a name="next-steps"></a>Nästa steg

[Översikt](overview.md)
