---
title: Guide för utvecklare för Azure IoT Hub | Microsoft Docs
description: Azure IoT Hub Developer Guide innehåller diskussioner om slut punkter, säkerhet, identitets registret, enhets hantering, direkta metoder, enhets-och fil överföringar, jobb, språk för IoT Hub frågor och meddelande hantering.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 1fcf560ba1fef137a28ab1872635cc2182cd07e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009206"
---
# <a name="azure-iot-hub-developer-guide"></a>Guide för Azure IoT Hub-utvecklare

Azure IoT Hub är en fullständigt hanterad tjänst som hjälper till att möjliggöra tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och Server delen av lösningen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Med Azure IoT Hub får du:

* Säker kommunikation genom att använda säkerhets referenser för varje enhet och åtkomst kontroll.

* Flera alternativ för kommunikation mellan enheter och moln och från moln till enhet.

* Fråge bara lagring av tillståndsinformation per enhet och meta-data.

* Enkel enhets anslutning med enhets bibliotek för de mest populära språken och plattformarna.

Den här guiden för IoT Hub utvecklare innehåller följande artiklar:

* [Med kommunikation från enhet till molnet](iot-hub-devguide-d2c-guidance.md) kan du välja mellan enhets-till-moln-meddelanden, enhetens dubbla rapporter och fil uppladdning.

* [Med kommunikation från moln till enhet](iot-hub-devguide-c2d-guidance.md) får du hjälp att välja mellan direkta metoder, enhetens dubbla och önskade egenskaper och meddelanden från molnet till enheten.

* [Meddelanden från enhet till moln och moln-till-enhet med IoT Hub](iot-hub-devguide-messaging.md) beskriver de meddelande funktioner (enhet-till-moln och moln-till-enhet) som IoT Hub visar.

  * [Skicka meddelanden från enheten till molnet till IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Läsa meddelanden från enheten till molnet från den inbyggda slut punkten](iot-hub-devguide-messages-read-builtin.md).

  * [Använd anpassade slut punkter och routningsregler för meddelanden från enhet till moln](iot-hub-devguide-messages-read-custom.md).

  * [Skicka meddelanden från moln till enhet från IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Skapa och läs IoT Hub meddelanden](iot-hub-devguide-messages-construct.md).

* [Ladda upp filer från en enhet](iot-hub-devguide-file-upload.md) beskriver hur du kan ladda upp filer från en enhet. Artikeln innehåller också information om ämnen, till exempel de meddelanden som överförings processen kan skicka.

* [Hantera enhets identiteter i IoT Hub](iot-hub-devguide-identity-registry.md) beskriver vilken information varje IoT Hub-identitets register lagrar. Artikeln beskriver också hur du kan komma åt och ändra den.

* [Kontrol lera åtkomst till IoT Hub](iot-hub-devguide-security.md) beskriver den säkerhets modell som används för att bevilja åtkomst till IoT Hub funktioner för både enheter och moln komponenter. Artikeln innehåller information om hur du använder tokens och X. 509-certifikat, samt information om de behörigheter som du kan bevilja.

* [Använd enhetens dubblare för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md) beskriver *enhetens dubbla* koncept. Artikeln beskriver också hur enhets enheten kan exponeras, t. ex. att synkronisera en enhet med enheten. Artikeln innehåller information om de data som lagras på en enhet med dubbla.

* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md) som beskriver livs cykeln för en direkt metod. Artikeln beskriver hur du anropar metoder på en enhet från din backend-app och hanterar den direkta metoden på enheten.

* [Schemalägg jobb på flera enheter](iot-hub-devguide-jobs.md) beskriver hur du kan schemalägga jobb på flera enheter. I artikeln beskrivs hur du skickar jobb som utför uppgifter som att köra en direkt metod, uppdatera en enhet med hjälp av en enhet med dubbla. Det beskriver också hur du frågar efter status för ett jobb.

* [Referens-Välj ett kommunikations protokoll](iot-hub-devguide-protocols.md) beskriver de kommunikations protokoll som IoT Hub stöder för enhets kommunikation och visar en lista över de portar som ska vara öppna.

* [Referens – IoT Hub slut punkter](iot-hub-devguide-endpoints.md) beskriver de olika slut punkter som varje IoT-hubb visar för körnings-och hanterings åtgärder. Artikeln beskriver också hur du kan skapa ytterligare slut punkter i din IoT-hubb och hur du använder en fält-Gateway för att aktivera anslutningar till dina IoT Hub slut punkter i andra situationer än standard.

* [Referens-IoT Hub frågespråk för enhets-, jobb-och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver det IoT Hub frågespråk som gör att du kan hämta information från hubben om din enhets dubblare och jobb.

* [Referens kvoter och begränsning](iot-hub-devguide-quotas-throttling.md) sammanfattar de kvoter som anges i IoT Hub tjänsten och den begränsning som inträffar när du överskrider en kvot.

* [Referens – prissättning](iot-hub-devguide-pricing.md) ger allmän information om olika SKU: er och priser för IoT Hub och information om hur de olika IoT Hub funktionerna mäts som meddelanden av IoT Hub.

* [Referens – enhets-och tjänst-SDK](iot-hub-devguide-sdks.md) : er visar Azure IoT-SDK: er för att utveckla enhets-och tjänst program som interagerar med IoT Artikeln innehåller länkar till API-dokumentation online.

* [Referens-IoT Hub MQTT support](iot-hub-mqtt-support.md) innehåller detaljerad information om hur IoT Hub stöder MQTT-protokollet. Artikeln beskriver stödet för MQTT-protokollet som är inbyggt i Azure IoT-SDK: er och ger information om hur du använder MQTT-protokollet direkt.
