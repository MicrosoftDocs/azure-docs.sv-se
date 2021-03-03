---
title: Skriv createOptions för moduler – Azure IoT Edge | Microsoft Docs
description: Så här använder du createOptions i distributions manifestet för att konfigurera moduler vid körning
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 055b89858fde901ab014e409fbe30c3438efce12
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732997"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Konfigurera behållar skapande alternativ för IoT Edge moduler

Med parametern **createOptions** i distributions manifestet kan du konfigurera modulens behållare vid körning. Den här parametern utökar kontrollen över modulerna och gör det möjligt för uppgifter som att tillåta eller begränsa modulens åtkomst till värd enhetens resurser, eller konfigurera nätverk.

IoT Edge moduler implementeras som Docker-kompatibla behållare på din IoT Edge-enhet. Docker erbjuder många alternativ för att skapa behållare, och dessa alternativ gäller även för IoT Edge moduler. Mer information finns i alternativ för att [skapa Docker-behållare](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Skapa alternativ för format

IoT Edge distributions manifestet accepterar skapande alternativ som är formaterade som JSON. Ta till exempel de skapande alternativ som automatiskt inkluderas för varje edgeHub-modul:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

I det här edgeHub-exemplet används parametern **HostConfig. PortBindings** för att mappa exponerade portar på behållaren till en port på värd enheten.

Om du använder tilläggen för Azure IoT-verktyg för Visual Studio eller Visual Studio Code, kan du skriva alternativen för att skapa i JSON-format i filen **deployment.template.js** . När du sedan använder tillägget för att skapa IoT Edge-lösningen eller genererar distributions manifestet, kommer det att stringify JSON åt dig i det format som IoT Edge runtime förväntar sig. Exempel:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Ett tips för att skriva skapande alternativ är att använda `docker inspect` kommandot. Som en del av utvecklings processen kör du modulen lokalt med `docker run <container name>` . När modulen fungerar som du vill ha den, kör du `docker inspect <container name>` . Det här kommandot matar ut informationen i modulen i JSON-format. Hitta de parametrar som du har konfigurerat och kopiera JSON. Exempel:

[![Resultat från Docker-inspektion av edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Vanliga scenarier

Alternativen för att skapa behållare möjliggör många scenarier, men här är några av de vanligaste när du skapar IoT Edge lösningar:

* [Ge moduler åtkomst till värd lagring](how-to-access-host-storage-from-module.md)
* [Mappa värd porten till modul porten](#map-host-port-to-module-port)
* [Begränsa minnesmodulens minne och CPU-användning](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mappa värd porten till modul porten

Om modulen behöver kommunicera med en tjänst utanför IoT Edge-lösningen och inte använder meddelanderoutning för att göra det, måste du mappa en värd port till en modul-port.

>[!TIP]
>Den här port mappningen krävs inte för modul-till-modul-kommunikation på samma enhet. Om modul A behöver fråga ett API som finns i modul B, kan det göra det utan någon port mappning. Modul B måste exponera en port i dess Dockerfile, till exempel: `EXPOSE 8080` . Sedan kan modul A fråga API: et med hjälp av modul B: s namn, till exempel: `http://ModuleB:8080/api` .

Se först till att en port i modulen exponeras för att lyssna efter anslutningar. Du kan göra detta med hjälp av en [Visa](https://docs.docker.com/engine/reference/builder/#expose) -instruktion i Dockerfile. Till exempel `EXPOSE 8080`. Den exponerande instruktionen använder som standard TCP-protokoll om inget värde anges eller om du kan ange UDP.

Använd sedan inställningen **PortBindings** i gruppen **HostConfig** i Docker-behållaren för att [skapa alternativ](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) för att mappa exponerade portar i modulen till en port på värd enheten. Om du till exempel exponerar Port 8080 i modulen och vill mappa den till port 80 av värd enheten, skulle skapande alternativen i template.jspå filen se ut som i följande exempel:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

När stringified för distributions manifestet, skulle samma konfiguration se ut som i följande exempel:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Begränsa minnesmodulens minne och CPU-användning

Du kan deklarera hur mycket av värd resurserna en modul kan använda. Den här kontrollen är användbar för att säkerställa att en modul inte kan använda för mycket minne eller CPU-användning och förhindrar att andra processer körs på enheten. Du kan hantera dessa inställningar med [Docker-behållare skapa alternativ](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) i **HostConfig** -gruppen, inklusive:

* **Minne**: minnes gräns i byte. Till exempel 268435456 byte = 256 MB.
* **MemorySwap**: total minnes gräns (minne och växling). Till exempel 536870912 byte = 512 MB
* **CpuPeriod**: längden på en processor period i mikrosekunder. Standardvärdet är 100000, så till exempel värdet 25000 begränsar en behållare till 25% av processor resurserna.

I template.jsi format skulle dessa värden se ut som i följande exempel:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

När stringified för det slutliga distributions manifestet, skulle dessa värden se ut som i följande exempel:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Nästa steg

Fler exempel på hur du skapar alternativ i åtgärder finns i följande IoT Edge exempel:

* [Custom Vision och Azure IoT Edge på en Raspberry Pi 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge Blob Storage-exempel](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
