---
title: Azure IoT Hub utveckla för begränsade enheter med IoT Hub C SDK
description: 'Guide för utvecklare – vägledning om hur du utvecklar med Azure IoT SDK: er för begränsade enheter.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5a43eb2537ebc09ffcb524a4426d7a8c9bec560b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500010"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Utveckla för begränsade enheter med hjälp av Azure IoT C SDK

Azure IoT Hub C SDK är skrivet i ANSI C (C99), vilket gör det lämpligt att använda olika plattformar med små disk-och minnes minnen. Det rekommenderade RAM-minnet är minst 64 KB, men det exakta minnes utrymmet beror på vilket protokoll som används, antalet anslutningar som har öppnats, samt den plattform som är riktad mot målet.
> [!NOTE]
> * Azure IoT C SDK publicerar regelbundet resurs förbruknings information som hjälper dig att utveckla.  Besök vår [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) och granska den senaste Benchmarken.
>

C SDK är tillgängligt i Package form från apt-get, NuGet och MBED. Om du vill rikta in dig på begränsade enheter kanske du vill bygga SDK lokalt för din mål plattform. Den här dokumentationen visar hur du tar bort vissa funktioner för att minska utrymmet på C SDK med [cmake](https://cmake.org/). Dessutom diskuterar den här dokumentationen de bästa praxis programmerings modellerna för att arbeta med begränsade enheter.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Skapa C SDK för begränsade enheter

Bygg C SDK för begränsade enheter.

> [!NOTE]
> Embedded C SDK är ett alternativ för begränsade enheter som stöder BYON-metoden (ta med din egen nätverks lösning). IoT-utvecklare har frihet att ta MQTT-klienten, TLS och socket som de väljer för att skapa en enhets lösning. [Läs mer om Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

### <a name="prerequisites"></a>Förutsättningar

Följ den här [installations guiden för C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) för att förbereda utvecklings miljön för att bygga C SDK. Innan du kommer till steget för att skapa med cmake kan du anropa cmake-flaggor för att ta bort oanvända funktioner.

### <a name="remove-additional-protocol-libraries"></a>Ta bort ytterligare protokoll bibliotek

C SDK har stöd för fem protokoll idag: MQTT, MQTT över WebSocket, AMQPs, AMQP över WebSocket och HTTPS. De flesta scenarier kräver ett till två protokoll som körs på en klient, och därför kan du ta bort protokoll biblioteket som du inte använder från SDK: n. Mer information om hur du väljer lämpligt kommunikations protokoll för ditt scenario finns i [Välj ett IoT Hub kommunikations protokoll](iot-hub-devguide-protocols.md). Till exempel är MQTT ett Lightweight-protokoll som ofta passar bättre för begränsade enheter.

Du kan ta bort AMQP-och HTTP-biblioteken med följande cmake-kommando:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Ta bort funktioner för SDK-loggning

C SDK tillhandahåller omfattande loggning i för att hjälpa till med fel sökning. Du kan ta bort loggnings funktionen för produktions enheter med följande cmake-kommando:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Ta bort uppladdning till BLOB-kapacitet

Du kan överföra stora filer till Azure Storage med hjälp av den inbyggda funktionen i SDK: n. Azure IoT Hub fungerar som en dispatcher till ett associerat Azure Storage-konto. Du kan använda den här funktionen för att skicka mediefiler, stora telemetri och loggar. Du kan få mer information om att [Ladda upp filer med IoT Hub](iot-hub-devguide-file-upload.md). Om programmet inte kräver den här funktionen kan du ta bort den här funktionen med följande cmake-kommando:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Köra remsa i Linux-miljö

Om dina binärfiler körs på Linux-systemet kan du använda [kommandot rand](https://en.wikipedia.org/wiki/Strip_(Unix)) för att minska storleken på det slutliga programmet efter kompileringen.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programmerings modeller för begränsade enheter

Sedan tittar du på programmerings modeller för begränsade enheter.

### <a name="avoid-using-the-serializer"></a>Undvik att använda serialiseraren

C SDK har en valfri [c SDK-serialisering](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)som gör att du kan använda deklarativ mappnings tabeller för att definiera metoder och enhetens dubbla egenskaper. Serialiseraren är utformad för att förenkla utvecklingen, men den lägger till overhead, vilket inte är optimalt för begränsade enheter. I det här fallet bör du överväga att använda primitiva klient-API: er och parsa JSON med hjälp av en förenklad parser som [Parson](https://github.com/kgabis/parson)

### <a name="use-the-lower-layer-_ll_"></a>Använd det nedre lagret (_lla_)

C SDK stöder två programmerings modeller. En uppsättning har API: er med en _lla_ infix, som står för ett lägre lager. Den här uppsättningen API: er är ljusare vikt och skapar inte arbets trådar, vilket innebär att användaren måste manuellt styra schemaläggning. För enhets klienten finns t. ex. de _lla_ API: erna i [huvud filen](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

En annan uppsättning API: er utan _lla_ index kallas för bekvämlighets skiktet, där en arbets tråd är Spuninst automatiskt. Till exempel kan du hitta API: er för snabb lager för enhets klienten i den här [klient huvud filen för IoT-enheten](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). För begränsade enheter där varje extra tråd kan ta en betydande del av system resurserna kan du överväga att använda _lla_ API: er.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure IoT C SDK-arkitekturen:
-    [Käll kod för Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/)
-    [Introduktion till Azure IoT Device SDK för C](iot-hub-device-sdk-c-intro.md)
