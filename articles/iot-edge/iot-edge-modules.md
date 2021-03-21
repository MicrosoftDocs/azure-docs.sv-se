---
title: Lär dig hur moduler kör logik på dina enheter – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge moduler är behållare för logiska enheter som kan distribueras och hanteras via en fjärr anslutning så att du kan köra affärs logik på IoT Edge enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492378"
---
# <a name="understand-azure-iot-edge-modules"></a>Förstå Azure IoT Edge-moduler

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Med Azure IoT Edge kan du distribuera och hantera affärs logik på kanten i form av *moduler*. Azure IoT Edge moduler är den minsta beräknings enheten som hanteras av IoT Edge och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen lösnings-specifika kod. Om du vill förstå hur moduler utvecklas, distribueras och hanteras, bör du tänka på de fyra koncept elementen i en modul:

* En **modul avbildning** är ett paket som innehåller den program vara som definierar en modul.
* En **modul instans** är den enhet av beräkning som kör-modulens avbildning på en IoT Edge enhet. Module-instansen startas av IoT Edge Runtime.
* En **modul identitet** är en del av informationen (inklusive säkerhets referenser) som lagras i IoT Hub, som är kopplad till varje instans i modulen.
* En **modul med dubbla** är ett JSON-dokument som lagras i IoT Hub, som innehåller Tillståndsinformation för en modulreferens, inklusive metadata, konfigurationer och villkor.

## <a name="module-images-and-instances"></a>Modul bilder och instanser

IoT Edge module-avbildningar innehåller program som utnyttjar funktionerna för hantering, säkerhet och kommunikation i IoT Edge Runtime. Du kan utveckla dina egna modulblad eller exportera en från en Azure-tjänst som stöds, t. ex. Azure Stream Analytics.
Avbildningarna finns i molnet och de kan uppdateras, ändras och distribueras i olika lösningar. Till exempel finns en modul som använder Machine Learning till att förutsäga produktions rads utdata som en separat avbildning än en modul som använder dator vision för att kontrol lera en drönare.

Varje gång en modul-avbildning distribueras till en enhet och startas av IoT Edge runtime skapas en ny instans av modulen. Två enheter i olika delar av världen kan använda samma modul-avbildning. Varje enhet skulle dock ha sin egen modul-instans när modulen startas på enheten.

![Diagram – modul bilder i molnet, modul instanser på enheter](./media/iot-edge-modules/image_instance.png)

I implementeringen finns moduler avbildningar som behållar avbildningar i en lagrings plats, och module-instanser är behållare på enheter.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modul identiteter

När en ny module-instans skapas av IoT Edge runtime, får den en motsvarande modul identitet. Modulens identitet lagras i IoT Hub och används som adress-och säkerhets omfattning för all lokal kommunikation och moln kommunikation för den module-instansen.

Den identitet som är kopplad till en modul är beroende av identiteten på den enhet där instansen körs och det namn som du anger för modulen i din lösning. Om du till exempel anropar `insight` en modul som använder en Azure Stream Analytics och distribuerar den på en enhet som kallas `Hannover01` , skapar IoT Edge runtime en motsvarande modul identitet `/devices/Hannover01/modules/insight` .

I scenarier när du behöver distribuera en modul-avbildning flera gånger på samma enhet kan du dessutom distribuera samma avbildning flera gånger med olika namn.

![Diagram – modul identiteter är unika inom enheter och mellan enheter](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Modul, dubbla

Varje instans i en modul har också en motsvarande modul som du kan använda för att konfigurera module-instansen. Instansen och den dubbla är kopplade till varandra via modulens identitet.

En modul dubbla är ett JSON-dokument som lagrar information om moduler och konfigurations egenskaper. Det här konceptet parallellt av [enhetens dubbla](../iot-hub/iot-hub-devguide-device-twins.md) koncept från IoT Hub. Strukturen i en modul är på samma sätt som en enhet, med dubbla. De API: er som används för att interagera med båda typerna av dubbla är också identiska. Den enda skillnaden mellan de två är den identitet som används för att instansiera klient-SDK: n.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Offlinefunktioner

Azure IoT Edge moduler kan köras offline i oändlighet efter synkronisering med IoT Hub minst en gång. IoT Edge-enheter kan också utöka den här offline-funktionen till andra IoT-enheter. Mer information finns i [förstå utökade offline-funktioner för IoT Edge enheter, moduler och underordnade enheter](offline-capabilities.md).

## <a name="next-steps"></a>Nästa steg

* [Förstå kraven och verktygen för att utveckla IoT Edge moduler](module-development.md)
* [Förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md)
