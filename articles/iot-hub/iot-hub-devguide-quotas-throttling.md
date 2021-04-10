---
title: Förstå Azure IoT Hub kvoter och begränsning | Microsoft Docs
description: Guide för utvecklare – Beskrivning av de kvoter som gäller för IoT Hub och det förväntade begränsnings beteendet.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: Operations'
- 'Role: Technical Support'
ms.openlocfilehash: 4b65d42522f40eb7d0e65356223313a924de3039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656999"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referens – IoT Hub kvoter och begränsning

Den här artikeln beskriver kvoterna för en IoT Hub och innehåller information som hjälper dig att förstå hur begränsningen fungerar.

## <a name="quotas-and-throttling"></a>Kvoter och begränsningar

Varje Azure-prenumeration kan ha högst 50 IoT-hubbar och högst en kostnads fri hubb.

Varje IoT-hubb etableras med ett visst antal enheter på en specifik nivå. Nivån och antalet enheter avgör den högsta dagliga kvoten för meddelanden som du kan skicka. Den meddelande storlek som används för att beräkna den dagliga kvoten är 0,5 KB för en kostnads fri nivå hubb och 4KB för alla andra nivåer. Mer information finns i [priser för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

Nivån avgör också de begränsnings gränser som IoT Hub tillämpas på alla åtgärder.

## <a name="iot-plug-and-play"></a>Plug and Play för IoT

IoT Plug and Play-enheter skickar minst ett telemetri-meddelande för varje gränssnitt, inklusive roten, vilket kan öka antalet meddelanden som räknas mot din meddelande kvot.

## <a name="operation-throttles"></a>Åtgärds begränsningar

Åtgärds begränsningar är hastighets begränsningar som tillämpas i minut intervall och är avsedda att förhindra missbruk. De är också föremål för [trafik form givning](#traffic-shaping).

I följande tabell visas de tvingade begränsningarna. Värden refererar till en enskild hubb.

| Begränsning | Gratis, B1 och S1 | B2 och S2 | B3 och S3 | 
| -------- | ------- | ------- | ------- |
| [Identitets register åtgärder](#identity-registry-operations-throttle) (skapa, Hämta, lista, uppdatera, ta bort) | 1.67 per sekund/enhet (100 per minut/enhet) | 1.67 per sekund/enhet (100 per minut/enhet) | 83.33 per sekund per enhet (5000 per minut/enhet) |
| [Nya enhets anslutningar](#device-connections-throttle) (denna gräns gäller för antalet _nya anslutningar_, inte det totala antalet anslutningar) | Större än 100 per sekund eller 12 per sekund/enhet <br/> Till exempel är två S1-enheter 2 \* 12 = 24 nya anslutningar/SEK, men du har minst 100 nya anslutningar/SEK över dina enheter. Med nio S1-enheter har du 108 nya anslutningar/SEK (9 \* 12) över dina enheter. | 120 nya anslutningar/SEK/enhet | 6 000 nya anslutningar/SEK/enhet |
| Sändningar enhet-till-moln | Högst 100 sändnings åtgärder/SEK eller 12 sändnings åtgärder/SEK/enhet <br/> Till exempel är två S1-enheter 2 \* 12 = 24/SEK, men du har minst 100 skicka-åtgärder per sekund för dina enheter. Med nio S1-enheter har du 108 skicka åtgärder/SEK (9 \* 12) över dina enheter. | 120 skicka åtgärder/SEK/enhet | 6 000 skicka åtgärder/SEK/enhet |
| Skicka från moln till enhet<sup>1</sup> | 1,67 skicka åtgärder/SEK/enhet (100 meddelanden/min/enhet) | 1,67 skicka åtgärder/SEK/enhet (100 skicka åtgärder/min/enhet) | 83,33 skicka åtgärder/SEK/enhet (5 000 skicka åtgärder/min/enhet) |
| Ta emot från moln till enhet<sup>1</sup> <br/> (endast när enheten använder HTTPS)| 16,67 Receive-åtgärder/SEK/enhet (1 000 Receive-åtgärder/min/enhet) | 16,67 Receive-åtgärder/SEK/enhet (1 000 Receive-åtgärder/min/enhet) | 833,33 Receive-åtgärder/SEK/enhet (50 000 Receive-åtgärder/min/enhet) |
| Fil uppladdning | 1,67 fil överförings initieringar/SEK per enhet (100 per minut/enhet) | 1,67 fil överförings initieringar/SEK per enhet (100 per minut/enhet) | 83,33 fil överförings initieringar/SEK per enhet (5000 per minut/enhet) |
| Direkta metoder<sup>1</sup> | 160KB per sekund/enhet<sup>2</sup> | 480KB per sekund/enhet<sup>2</sup> | 24MB per sekund/enhet<sup>2</sup> | 
| Frågor | 20 per minut/enhet | 20 per minut/enhet | 1000 per minut/enhet |
| Dubbla (enhet och modul) läser<sup>1</sup> | 100/SEK | Högre på 100/SEK eller 10/SEK/per enhet | 500 per sekund per enhet |
| Dubbla uppdateringar (enhet och modul)<sup>1</sup> | 50/SEK | Högre av 50/SEK eller 5/SEK/per enhet | 250/SEK/enhet |
| Jobb åtgärder<sup>1</sup> <br/> (skapa, uppdatera, visa, ta bort) | 1.67 per sekund/enhet (100 per minut/enhet) | 1.67 per sekund/enhet (100 per minut/enhet) | 83.33 per sekund per enhet (5000 per minut/enhet) |
| Jobb enhets åtgärder<sup>1</sup> <br/> (uppdatera dubbla, anropa Direct-metod) | 10 per sekund | Större än 10 per sekund eller 1/SEK/enhet | 50 per sekund per enhet |
| Konfigurationer och Edge-distributioner<sup>1</sup> <br/> (skapa, uppdatera, visa, ta bort) | 0.33 per sekund/enhet (20 per minut/enhet) | 0.33 per sekund/enhet (20 per minut/enhet) | 0.33 per sekund/enhet (20 per minut/enhet) |
| Enhets Ströms initierings frekvens<sup>1</sup> | 5 nya strömmar/SEK | 5 nya strömmar/SEK | 5 nya strömmar/SEK |
| Maximalt antal anslutna enhets strömmar<sup>1</sup> | 50 | 50 | 50 |
| Maximal enhets data överföring<sup>1</sup> (sammanställd volym per dag) | 300 MB | 300 MB | 300 MB |

<sup>1</sup> Den här funktionen är inte tillgänglig på den grundläggande IoT Hubs nivån. Mer information finns i [så här väljer du rätt IoT Hub](iot-hub-scaling.md). <br/><sup>2</sup> Begränsnings mätarens storlek är 4 KB. Begränsningen baseras endast på begär ande nytto Last storlek.

### <a name="throttling-details"></a>Begränsnings information

* Mätar storleken avgör vid vilket steg din begränsnings gräns förbrukas. Om din direkta samtals nytto Last är mellan 0 och 4 KB räknas det som 4 KB. Du kan ringa upp till 40 samtal per sekund per enhet innan du når gränsen på 160 KB/SEK/s/enhet.

   På samma sätt gäller att om nytto lasten är mellan 4 KB och 8 KB, varje anrops konto för 8 KB och du kan ringa upp till 20 anrop per sekund per enhet innan du når Max gränsen.

   Slutligen, om nytto Last storleken är mellan 156KB och 160 KB, kommer du bara att kunna ringa 1 per sekund per enhet i hubben innan du når gränsen på 160 KB/s/Unit.

*  För *jobb enhets åtgärder (uppdatering, dubbla, Invoke Direct-metod)* för nivå S3, gäller 50/SEK/enheten bara när du anropar metoder med hjälp av jobb. Om du anropar direkta metoder direkt gäller den ursprungliga begränsnings gränsen på 24 MB/SEK/per enhet (för S3).

*  **Kvot** är det sammanlagda antalet meddelanden som du kan skicka i hubben *per dag*. Du kan hitta hubbens kvot gräns under kolumnen **Totalt antal meddelanden som per dag** på [sidan IoT Hub prissättning](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Dina begränsningar för din moln-till-enhet och enhet till moln avgör hur *ofta* du kan skicka meddelanden – antal meddelanden, oavsett om det är 4 KB-segment. Varje meddelande kan vara upp till 256 KB, vilket är den [största meddelande storleken](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Det är en bra idé att begränsa dina samtal så att du inte når/överskrider begränsnings gränserna. Om du når gränsen svarar IoT Hub med felkod 429 och klienten bör stängas av och sedan försöka igen. Dessa gränser är per hubb (eller i vissa fall per hubb/enhet). Mer information finns i [hantera anslutningar och Reliable Messaging/retry-mönster](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Trafik utformning

För att kunna hantera burst-trafik accepterar IoT Hub begär Anden ovanför begränsningen under en begränsad tid. De första av dessa begär Anden bearbetas omedelbart. Men om antalet förfrågningar fortsätter strider mot begränsningen börjar IoT Hub att placera begär anden i en kö och bearbetas enligt begränsnings hastigheten. Den här effekterna kallas för *trafik form*. Dessutom är storleken på den här kön begränsad. Om begränsnings överträdelsen fortsätter kan kön fyllas och IoT Hub börjar avvisa begär Anden med `429 ThrottlingException` .

Du kan till exempel använda en simulerad enhet för att skicka 200 enhet-till-moln-meddelanden per sekund till din S1-IoT Hub (som har en gräns på 100/SEK D2C-sändningar). För den första minuten eller två bearbetas meddelandena direkt. Men eftersom enheten fortsätter att skicka fler meddelanden än begränsnings gränsen börjar IoT Hub endast bearbeta 100 meddelanden per sekund och placerar resten i en kö. Du börjar märker ökad svars tid. Slutligen börjar du få `429 ThrottlingException` medan kön fyller upp och ["antalet begränsnings fel" IoT Hub måttet](monitor-iot-hub-reference.md#device-telemetry-metrics) börjar öka. Information om hur du skapar aviseringar och diagram baserat på mått finns i [övervaka IoT Hub](monitor-iot-hub.md).

### <a name="identity-registry-operations-throttle"></a>Åtgärds begränsningar för identitets registret

Åtgärder för enhets identitets registret är avsedda för körnings användning i enhets hantering och etablerings scenarier. Läsning eller uppdatering av ett stort antal enhets identiteter stöds via [import-och export jobb](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

När du initierar identitets åtgärder via [Mass register uppdaterings åtgärder](https://docs.microsoft.com/rest/api/iothub/service/bulkregistry/updateregistry) (*inte* Mass import-och export jobb) gäller samma begränsnings gränser. Om du till exempel vill skicka en Mass åtgärd för att skapa 50-enheter och du har en S1-IoT Hub med 1 enhet godkänns bara två av dessa Mass begär Anden per minut. Detta beror på att identitets åtgärden för en S1-IoT Hub med 1 enhet är 100 per minut/enhet. I det här fallet skulle en tredje begäran (och mer) på samma minut nekas eftersom gränsen redan hade uppnåtts. 

### <a name="device-connections-throttle"></a>Begränsning av enhets anslutningar

Begränsningen för *enhets anslutningar* styr den hastighet med vilken nya enhets anslutningar kan upprättas med en IoT-hubb. Begränsningen för *enhets anslutningar* styr inte det maximala antalet anslutna enheter samtidigt. Hastighets begränsningen för *enhets anslutningar* beror på antalet enheter som är etablerade för IoT Hub.

Om du till exempel köper en enskild S1-enhet får du en begränsning på 100 anslutningar per sekund. För att ansluta 100 000-enheter tar det därför minst 1 000 sekunder (cirka 16 minuter). Du kan dock ha lika många anslutna enheter samtidigt som du har registrerade enheter i identitets registret.

## <a name="other-limits"></a>Andra gränser

IoT Hub tillämpar andra drift gränser:

| Åtgärd | Gräns |
| --------- | ----- |
| Enheter | Det totala antalet enheter plus moduler som kan registreras till en enda IoT-hubb är ett tak på 1 000 000. Det enda sättet att öka den här gränsen är att kontakta [Microsoft Support](https://azure.microsoft.com/support/options/).|
| Fil överföringar | 10 samtidiga fil överföringar per enhet. |
| Jobb<sup>1</sup> | Maximalt antal samtidiga jobb är 1 (kostnads fritt och S1), 5 (för S2) och 10 (för S3). Dock är det maximala antalet samtidiga [enhets import/export-jobb](iot-hub-bulk-identity-mgmt.md) 1 för alla nivåer. <br/>Jobb historiken behålls upp till 30 dagar. |
| Ytterligare slut punkter | Betalda SKU-hubbar kan ha 10 ytterligare slut punkter. De kostnads fria SKU-hubbarna kan ha en ytterligare slut punkt. |
| Frågor för meddelanderoutning | Betalda SKU-hubbar kan ha 100-routnings frågor. De kostnads fria SKU-hubbarna kan ha fem Dirigerings frågor. |
| Meddelandeberikanden | Betalda SKU-hubbar kan ha upp till 10 meddelande berikare. De kostnads fria SKU-hubbarna kan ha upp till 2 meddelande berikare.|
| Meddelanden från enhet till moln | Maximal meddelande storlek 256 KB |
| Meddelanden från moln till enhet<sup>1</sup> | Maximal meddelande storlek 64 KB. Maximalt antal väntande meddelanden för leverans är 50 per enhet. |
| Direct-metod<sup>1</sup> | Maximal nytto Last storlek för direkt metod är 128 KB. |
| Automatisk enhets-och modul konfiguration<sup>1</sup> | 100 konfigurationer per betald SKU-hubb. 20 konfigurationer per kostnads fri SKU-hubb. |
| IoT Edge automatiska distributioner<sup>1</sup> | 50 moduler per distribution. 100 distributioner (inklusive lager distributioner) per betald SKU-hubb. 10 distributioner per kostnads fri SKU-hubb. |
| Dubblar<sup>1</sup> | Maximal storlek för önskade egenskaper och avsnitt med rapporterade egenskaper är 32 KB. Den maximala storleken på Tags-avsnittet är 8 KB. |
| Principer för delad åtkomst | Maximalt antal principer för delad åtkomst är 16. |
| x509 CA-certifikat | Det maximala antalet x509 CA-certifikat som kan registreras på IoT Hub är 25. |

<sup>1</sup> Den här funktionen är inte tillgänglig på den grundläggande IoT Hubs nivån. Mer information finns i [så här väljer du rätt IoT Hub](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Öka kvoten eller begränsningen

Du kan när som helst öka kvoterna eller begränsa gränserna genom att [öka antalet etablerade enheter i en IoT-hubb](iot-hub-upgrade.md).

## <a name="latency"></a>Svarstid

IoT Hub strävar efter att tillhandahålla låg latens för alla åtgärder. Men på grund av nätverks förhållanden och andra oförutsägbara faktorer kan den inte garantera en viss latens. När du utformar din lösning bör du:

* Undvik att göra några antaganden om den maximala svars tiden för en IoT Hub åtgärd.
* Etablera din IoT Hub i den Azure-region som är närmast dina enheter.
* Överväg att använda Azure IoT Edge för att utföra latens-känsliga åtgärder på enheten eller på en gateway nära enheten.

Flera IoT Hub enheter påverkar begränsningen enligt beskrivningen ovan, men ger inga ytterligare svars tider eller garantier.

Om du ser oväntade ökningar i åtgärds svars tiden kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

En djupgående Beskrivning av IoT Hub begränsnings beteende finns i blogg inlägget [IoT Hub begränsning och du](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Andra referens ämnen i den här IoT Hub Developer Guide är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md)
* [Övervaka IoT Hub](monitor-iot-hub.md)
