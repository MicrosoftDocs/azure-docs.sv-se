---
title: Introduktion till Azure IoT Hub | Microsoft Docs
description: Läs mer om Azure IoT Hub. Den här IoT-tjänsten har utformats för skalbar datapåfyllning, enhetshantering och säkerhet.
author: nberdy
ms.author: nberdy
ms.date: 08/08/2019
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 7e72f628bf328a3b93e81c60aa65b8c2495f7f2b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095266"
---
# <a name="what-is-azure-iot-hub"></a>Vad är Azure IoT Hub?

IoT-Hub är en hanterad tjänst som finns i molnet, och som fungerar som ett centralt meddelandenav för dubbelriktad kommunikation mellan din IoT-program och de enheter som den hanterar. Du kan använda Azure IoT Hub för att skapa IoT-lösningar med tillförlitlig och säker kommunikation mellan miljontals IoT-enheter och en molnbaserad serverdelslösning. Du kan ansluta nästan vilken enhet som helst till IoT Hub.

IoT Hub stöder kommunikation både från enheten till molnet och från molnet till enheten. IoT Hub stöder flera meddelandemönster, t.ex. enhet-till-moln-telemetri, filuppladdning från enheter och begäran-svars-metoder för att kontrollera dina enheter från molnet. IoT Hub-övervakning hjälper dig att upprätthålla din lösnings hälsa genom att spåra händelser som enhetskapande, enhetsfel och enhetsanslutningar.

IoT Hubs funktioner hjälper dig att skapa skalbara kompletta IoT-lösningar, som t.ex. att hantera industriell utrustning som används vid tillverkning, att spåra värdefulla tillgångar i hälsovård och övervaka användningen av kontorsbyggnader.

## <a name="scale-your-solution"></a>Skala din lösning

IoT Hub kan skalas till flera miljoner samtidiga anslutna enheter och flera miljoner händelser per sekund och därigenom stödja dina IoT-arbetsbelastningar. Mer information om hur du skalar IoT Hub finns i [IoT Hub skalning](iot-hub-scaling.md?branch=release-iotbasic). Om du vill veta mer om flera nivåer av tjänster som erbjuds av IoT Hub och hur du bäst passar dina skalbarhets behov kan du titta på [sidan med priser](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>Skydda din kommunikation

IoT Hub ger dig en säker kommunikationskanal genom vilken dina enheter kan skicka data.

* Autentisering per enhet innebär att varje enhet kan ansluta säkert till IoT Hub och att varje enhet kan hanteras på ett säkert sätt.

* Du har fullständig kontroll över enhetsåtkomst och kan kontrollera anslutningar på enhetsnivå.

* [IoT Hub-enhetsetableringstjänsten](../iot-dps/index.yml) tillhandahåller automatiskt enheter till rätt IoT Hub när enheten först startas.

* Flera autentiseringstyper stöder flera olika enhetsfunktioner:

  * SAS-tokenbaserad autentisering gör att du snabbt kan komma igång med din IoT-lösning.

  * Enskild X.509-certifikatautentisering för säker standardbaserad autentisering.

  * X.509-CA-autentisering för enkel, standardbaserad registrering.

## <a name="route-device-data"></a>Vägenhetsdata

Den inbyggda funktionen för meddelandedirigering ger dig flexibla möjligheter att konfigurera automatisk regelbaserad meddelandeförgrening:

* Använd [meddelanderoutning](iot-hub-devguide-messages-d2c.md) för att styra var hubben ska skicka enhets telemetri.

* Det innebär inte någon extra kostnad att skicka meddelanden till flera slutpunkter.

* Icke-kodroutningsregler ersätter anpassad dispatcherkod för meddelanden.

## <a name="integrate-with-other-services"></a>Integrera med andra tjänster

Du kan integrera IoT Hub med andra Azure-tjänster att skapa kompletta slutpunkt-till-slutpunkt-lösningar. Du kan t.ex. använda:

* [Azure Event Grid](../event-grid/index.yml) för att få din verksamhet att snabbt reagera på kritiska händelser på ett tillförlitligt, skalbart och säkert sätt.

* [Azure Logic Apps](../logic-apps/index.yml) för att automatisera verksamhetsprocesser.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) för att lägga till maskininlärning och AI-modeller i din lösning.

* [Azure Stream Analytics](../stream-analytics/index.yml) för att göra analytiska beräkningar i realtid för dataströmmar från dina enheter.

## <a name="configure-and-control-your-devices"></a>Konfigurera och kontrollera dina enheter

Du kan hantera dina enheter som är anslutna till IoT Hub med en matris med inbyggda funktioner.

* Lagra, synkronisera och skicka frågor till enhetsmetadata och tillståndsinformation för alla dina enheter.

* Ställ in enhetstillstånd per enhet eller baserat på enheters gemensamma egenskaper.

* Svara automatiskt på enhetsrapporterade tillståndsändringar med meddelanderoutningsintegration.

## <a name="make-your-solution-highly-available"></a>Gör din lösning mycket tillgänglig

Det finns ett [servicenivåavtal för IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/) på 99,9 %. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/) förklaras den garanterade tillgängligheten för Azure som helhet.

## <a name="connect-your-devices"></a>Anslut dina enheter

Använd [Azure IoT-enhet SDK](./iot-hub-devguide-sdks.md)-biblioteken för att bygga program som körs på dina enheter och interagerar med IoT Hub. Plattformar som stöds omfattar flera Linux-distributioner, Windows och realtidsoperativsystem. Språk som stöds:

* C
* Inbäddat C
* C#
* Java
* Python
* Node.js.

IoT Hub och enhets-SDK:erna stöder följande protokoll för att ansluta enheter:

* HTTPS
* AMQP
* AMQP över WebSockets
* MQTT
* MQTT över WebSockets

IoT Hub och enhets-SDK: er har stöd för [Azure IoT plug and Play](../iot-pnp/overview-iot-plug-and-play.md) -konventioner för att ansluta enheter. IoT Plug and Play-enheter använder en enhets modell för att annonsera sina funktioner till IoT Plug and Play-aktiverade program. Enhets modellen gör det möjligt för lösnings byggare att integrera smarta enheter med sina lösningar utan manuell konfiguration.

Om lösningen inte kan använda enhetsbiblioteken kan enheterna använda MQTT v3.1.1-, HTTPS 1.1- eller AMQP 1.0-protokoll för att ansluta internt till hubben.

Om din lösning inte kan använda något av de protokoll som stöds kan du utöka IoT Hub till att stödja anpassade protokoll:

* Använd [Azure IoT Edge](../iot-edge/index.yml) för att skapa en fältgateway om du vill utföra protokollöversättningar.

* Anpassa [Azure IoT-protokollgatewayen](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) så att den kan utföra protokollöversättning i molnet.

## <a name="quotas-and-limits"></a>Kvoter och begränsningar

Varje Azure-prenumeration har standardkvotgränser för att förhindra missbruk av tjänsten, och dessa begränsningar kan påverka IoT-lösningens omfång. Den aktuella gränsen för en per prenumeration är 50 IoT Hub per prenumeration. Du kan begära att kvoten ska ökas genom att kontakta supporten. Mer information finns i [IoT Hub kvoter och begränsning](iot-hub-devguide-quotas-throttling.md). Mer information om kvot gränser finns i någon av följande artiklar:

* [Begränsningar för Azure-prenumerations tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub-begränsning och du](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>IoT Hub på Azure Stack Hub (förhandsversion)

IoT Hub på Azure Stack Hub (för hands version) kan du skapa hybrid IoT-lösningar. IoT Hub är en hanterad tjänst som fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan IoT-programmet och de enheter som hanteras av. Du kan använda IoT Hub på Azure Stack Hub för att bygga IoT-lösningar med tillförlitlig och säker kommunikation mellan IoT-enheter och dina lokala lösningar.

IoT Hub på Azure Stack Hub är kostnads fritt under den offentliga för hands versionen. Mer information finns i [Översikt över IoT Hub i Azure Stack Hub](/azure-stack/operator/iot-hub-rp-overview).

## <a name="next-steps"></a>Nästa steg

Om du vill testa en IoT-lösning för slutpunkt-till-slutpunkt så ta en titt på snabbstarterna för IoT Hub:

* [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md)

Om du vill veta mer om hur du kan bygga och distribuera IoT-lösningar med Azure IoT går du till:

* [Grunderna: Azure IoT-tekniker och-lösningar](../iot-fundamentals/iot-services-and-technologies.md).