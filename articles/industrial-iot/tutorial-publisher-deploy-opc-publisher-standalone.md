---
title: Distribuera Microsoft OPC Publisher
description: I den här självstudien får du lära dig hur du distribuerar OPC-utgivaren i fristående läge.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787754"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Självstudie: Distribuera OPC-utgivaren

OPC Publisher är en Microsoft-produkt som stöds fullt ut, som har utvecklats i öppet, som förenar mellanrummet mellan industriella till gångar och Microsoft Azure molnet. Det gör det genom att ansluta till OPC UA-aktiverade till gångar eller industriella anslutnings program och publicera telemetridata till [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) i olika format, inklusive IEC62541 OPC UA PubSub standardformat (från version 2,6 och senare).

Den körs på [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) som en modul eller på en enkel Docker som en behållare. Eftersom den använder sig av [.net-plattforms oberoende körning](https://docs.microsoft.com/dotnet/core/introduction)körs den också internt i Linux och Windows 10.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Distribuera OPC-utgivaren
> * Kör den senaste utgivna versionen av OPC Publisher som en behållare
> * Ange alternativ för att skapa behållare i Azure Portal

Om du inte har en Azure-prenumeration kan du skapa ett kostnads fritt utvärderings konto

## <a name="prerequisites"></a>Förutsättningar

- Ett IoT Hub måste skapas
- En IoT Edge enhet måste skapas

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Distribuera OPC-utgivaren från Azure Marketplace

1. Välj den Azure-prenumeration som ska användas. Om ingen Azure-prenumeration är tillgänglig måste du skapa en.
2. Välj IoT Hub OPC-utgivaren ska skicka data till. Om ingen IoT Hub är tillgänglig måste du skapa en.
3. Välj den IoT Edge enhet som OPC-utgivaren ska köras på (eller ange ett namn för en ny IoT Edge-enhet som ska skapas).
4. Klicka på Skapa. Sidan "Ange moduler på enheten" för den valda IoT Edge enheten öppnas.
5. Klicka på "OPCPublisher" för att öppna sidan "uppdatera IoT Edge modul på OPC" och välj sedan "behållar skapande alternativ".
6. Ange ytterligare alternativ för att skapa behållare baserat på din användning av OPC Publisher, se nästa avsnitt nedan.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>Åtkomst till Microsoft Container Registry Docker-behållare för OPC-utgivare manuellt

Den senaste utgivna versionen av OPC Publisher kan köras manuellt via:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Där "name" är namnet på behållaren.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Ange alternativ för att skapa behållare i Azure Portal
När du distribuerar OPC-utgivare via Azure Portal kan du ange alternativen för att skapa behållare på sidan uppdatera IoT Edge modul i OPC Publisher. Dessa skapande alternativ måste vara i JSON-format. Kommando rads argumenten för OPC Publisher kan anges via CMD-nyckeln, t. ex.:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

En typisk uppsättning IoT Edge modul container Create options for OPC Publisher är:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Med de här alternativen angivna kommer OPC Publisher att läsa konfigurations filen `./pn.json` . Arbets katalogen för OPC-utgivaren är inställd på `/appdata` vid start och därför kommer OPC Publisher att läsa filen `/appdata/pn.json` i dess Docker-behållare. Logg filen för OPC-utgivaren skrivs till `/appdata` och `CertificateStores` katalogen (används för OPC UA-certifikat) skapas också i den här katalogen. För att göra dessa filer tillgängliga i IoT Edge Host File System, kräver container konfigurationen en bindnings volym för bindning. `/iiotedge:/appdata`Bindningen mappar katalogen `/appdata` till värd katalogen `/iiotedge` (som kommer att skapas av IoT Edge Runtime om den inte finns).
**Utan den här bindnings volymen för bindningen går alla konfigurationsfiler för OPC-utgivare förlorade när behållaren startas om.**

En anslutning till en OPC UA-server som använder sitt värdnamn utan en DNS-server som kon figurer ATS i nätverket kan uppnås genom att lägga till en `ExtraHosts` post i `HostConfig` avsnittet:

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Nästa steg 
Nu när du har distribuerat modulen OPC Publisher Edge är nästa steg att konfigurera den:

> [!div class="nextstepaction"]
> [Konfigurera OPC-utgivaren](tutorial-publisher-configure-opc-publisher.md)