---
title: Konfigurera Microsoft OPC Publisher
description: I den här självstudien får du lära dig hur du konfigurerar OPC-utgivaren i fristående läge.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787805"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Självstudie: Konfigurera OPC-utgivaren

I den här självstudien innehåller information om konfigurationen av OPC-utgivaren. Flera gränssnitt kan användas för att konfigurera det.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera OPC-utgivaren via konfigurations filen
> * Konfigurera OPC-utgivaren via kommando rads argument
> * Konfigurera OPC-utgivaren via IoT Hub direkta metoder
> * Konfigurera OPC-utgivaren via molnbaserad, medföljande REST mikrotjänst

## <a name="configuring-security"></a>Konfigurera säkerhet

IoT Edge tillhandahåller OPC-utgivare med säkerhets konfigurationen för att komma åt IoT Hub automatiskt. OPC Publisher kan också köras som en fristående Docker-behållare genom att ange en enhets anslutnings sträng för att få åtkomst till IoT Hub via `dc` kommando rads parametern. En enhet för IoT Hub kan skapas och dess anslutnings sträng hämtas via Azure Portal.

För att komma åt OPC UA-aktiverade till gångar används X. 509-certifikat och deras associerade privata nycklar av OPC UA. Detta kallas för OPC UA Application Authentication och förutom OPC UA-användarautentisering. OPC Publisher använder ett filsystem baserat certifikat Arkiv för att hantera alla program certifikat. Under starten kontrollerar OPC-utgivare om det finns ett certifikat som den kan använda i certifikat arkiven och skapar ett nytt självsignerat certifikat och en ny associerad privat nyckel om det inte finns någon. Självsignerade certifikat ger svag autentisering eftersom de inte har signerats av en betrodd certifikat utfärdare, men det går att kryptera det här sättet minst kommunikationen med OPC UA-aktiverad till gång.

Säkerhet är aktiverat i konfigurations filen via- `"UseSecurity": true,` flaggan. Den mest säkra slut punkten som är tillgänglig på OPC UA-servrar som OPC-utgivaren ska ansluta till väljs automatiskt.
Som standard använder OPC Publisher Anonym användarautentisering (i tillägg till den programautentisering som beskrivs ovan). OPC Publisher stöder dock även användarautentisering med användar namn och lösen ord. Detta kan anges via REST API konfigurations gränssnitt (beskrivs nedan) eller konfigurations filen enligt följande:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Dessutom krypteras användar namn och lösen ord i OPC Publisher version 2,5 och nedan i konfigurations filen. Version 2,6 och senare stöder endast användar namn och lösen ord i klartext. Detta kommer att förbättras i nästa version av OPC-utgivaren.

För att spara säkerhets konfigurationen av OPC-utgivaren vid omstarter måste certifikatet och den privata nyckeln som finns i certifikat Arkiv katalogen mappas till IoT Edge värd-OS-filsystem. Se "ange behållar skapande alternativ i Azure Portal" ovan.

## <a name="configuration-via-configuration-file"></a>Konfiguration via konfigurations fil

Det enklaste sättet att konfigurera OPC-utgivaren är via en konfigurations fil. Ett exempel på en konfigurations fil och dokumentation om dess format tillhandahålls via filen [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) i den här lagrings platsen.
Syntaxen för konfigurations filen har ändrats över tid och OPC kan fortfarande läsa gamla format, men konverterar dem till det senaste formatet När konfigurationen behålls, vilket görs regelbundet på ett automatiserat sätt.

En grundläggande konfigurations fil ser ut så här:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

OPC UA-tillgångar optimerar nätverks bandbredd genom att bara skicka data ändringar till OPC Publisher när data har ändrats. Om data ändringar behöver publiceras oftare eller med jämna mellanrum stöder OPC Publisher en "pulsslag" för varje konfigurerat data objekt som kan aktive ras genom att ange HeartbeatInterval-nyckeln i data objektets konfiguration. Intervallet anges i sekunder:
```
 "HeartbeatInterval": 3600,
```

En OPC UA-tillgång skickar alltid det aktuella värdet för ett data objekt när OPC Publisher först ansluter till det. För att förhindra att data publiceras till IoT Hub, kan SkipFirst-nyckeln dessutom anges i data objektets konfiguration:
```
 "SkipFirst": true,
```

Båda inställningarna kan aktive ras globalt via kommando rads alternativen.

## <a name="configuration-via-command-line-arguments"></a>Konfiguration via kommando rads argument

Det finns flera kommando rads argument som kan användas för att ange globala inställningar för OPC-utgivare. De beskrivs [här](reference-command-line-arguments.md).


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Konfiguration via det inbyggda OPC UA Server-gränssnittet

>[!NOTE] 
> Den här funktionen är endast tillgänglig i version 2,5 och senare av OPC-utgivare. * *

OPC Publisher har en inbyggd OPC UA-server som körs på port 62222. Den implementerar tre OPC UA-metoder:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

Det här gränssnittet kan nås via ett OPC UA-klientprogram, till exempel [UA-expert](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Konfiguration via IoT Hub direkta metoder

>[!NOTE] 
> Den här funktionen är endast tillgänglig i version 2,5 och senare av OPC-utgivare. * *

OPC Publisher implementerar följande [IoT Hub direkta metoder](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods), som kan anropas från ett program (från var som helst i världen) och utnyttjar [IoT Hub-enhetens SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks):

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

Vi har angett ett exempel på ett [konfigurations program](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) och ett [program för att läsa diagnostikinformation](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) från OPC utgivare med öppen källkod och utnyttjar det här gränssnittet.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Konfiguration via molnbaserad, medföljande REST mikrotjänst

>[!NOTE] 
> Den här funktionen är endast tillgänglig i version 2,6 och senare av OPC-utgivaren.

En molnbaserad, medföljande mikrotjänst med ett REST-gränssnitt beskrivs och finns [här](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md). Den kan användas för att konfigurera OPC-utgivare via ett OpenAPI-kompatibelt gränssnitt, t. ex. via Swagger.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Konfiguration av enkel JSON-telemetri via separat konfigurations fil

>[!NOTE] 
> Den här funktionen är endast tillgänglig i version 2,5 och senare av OPC-utgivaren.

Med OPC Publisher kan du filtrera delarna i det icke standardiserade, enkla telemetri formatet via en separat konfigurations fil som kan anges via kommando rads alternativet TC. Om ingen konfigurations fil anges skickas hela JSON-telemetri till IoT Hub. Formatet på den separata konfigurations filen för telemetri beskrivs [här](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format).

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat OPC-utgivaren är nästa steg att lära dig hur du finjusterar prestanda och minne för Edge-modulen:

> [!div class="nextstepaction"]
> [Prestanda-och minnes justering](tutorial-publisher-performance-memory-tuning-opc-publisher.md)