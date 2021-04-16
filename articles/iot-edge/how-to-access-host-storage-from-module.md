---
title: Använda IoT Edge lokal lagring från en modul – Azure IoT Edge | Microsoft Docs
description: Använd miljövariabler och skapa alternativ för att aktivera modulåtkomst till IoT Edge lokal lagring.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 78752d4da42fe07461ae0e82b10343dc7219ad91
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482066"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Ge moduler åtkomst till en enhets lokala lagring

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Förutom att lagra data med hjälp av Azure-lagringstjänster eller i enhetens containerlagring kan du även dedikera lagring på själva värddatorn IoT Edge för bättre tillförlitlighet, särskilt när du arbetar offline.

## <a name="link-module-storage-to-device-storage"></a>Länka modullagring till enhetslagring

Om du vill aktivera en länk från modullagring till lagringen på värdsystemet skapar du en miljövariabel för modulen som pekar på en lagringsmapp i containern. Använd sedan alternativen för att skapa för att binda lagringsmappen till en mapp på värddatorn.

Om du till exempel vill aktivera IoT Edge-hubben för att lagra meddelanden i enhetens lokala lagring och hämta dem senare kan du konfigurera miljövariablerna och skapa-alternativen i Azure Portal i **avsnittet Körningsinställningar.**

1. För både IoT Edge hub och IoT Edge-agenten lägger du till en miljövariabel med namnet **storageFolder** som pekar på en katalog i modulen.
1. För både IoT Edge och IoT Edge-agenten lägger du till bindningar för att ansluta en lokal katalog på värddatorn till en katalog i modulen. Exempel:

   ![Lägg till alternativ för att skapa och miljövariabler för lokal lagring](./media/how-to-access-host-storage-from-module/offline-storage.png)

Eller så kan du konfigurera den lokala lagringen direkt i distributionsmanifestet. Exempel:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Ersätt `<HostStoragePath>` och med din `<ModuleStoragePath>` värd- och modullagringssökväg. Båda värdena måste vara en absolut sökväg.

I ett Linux-system innebär till exempel att katalogen `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` **/etc/iotedge/storage** i värdsystemet mappas till katalogen **/iotedge/storage/** i containern. I ett Windows-system innebär ett annat exempel att katalog C: temp i värdsystemet mappas till katalogen `"Binds":["C:\\temp:C:\\contemp"]` **C: \\ förakt** i containern. **\\**

På Linux-enheter kontrollerar du dessutom att användarprofilen för din modul har nödvändiga läs-, skriv- och körningsbehörigheter till värdsystemkatalogen. Om du återgår till det tidigare exemplet på IoT Edge-hubben kan lagra meddelanden i enhetens lokala lagring måste du bevilja behörigheter till dess användarprofil, UID 1000. (IoT Edge-agenten fungerar som rot, så den behöver inte ytterligare behörigheter.) Det finns flera sätt att hantera katalogbehörigheter på Linux-system, inklusive att använda för att ändra katalogägaren och sedan ändra `chown` `chmod` behörigheterna, till exempel:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Du hittar mer information om att skapa alternativ från [Docker-dokument.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="encrypted-data-in-module-storage"></a>Krypterade data i modullagring

När moduler anropar IoT Edge daemonens arbetsbelastnings-API för att kryptera data härleds krypteringsnyckeln med modul-ID:t och modulens generations-ID. Ett generations-ID används för att skydda hemligheter om en modul tas bort från distributionen och en annan modul med samma modul-ID senare distribueras till samma enhet. Du kan visa en moduls generations-ID med hjälp av Azure [CLI-kommandot az iot hub module-identity show](/cli/azure/iot/hub/module-identity).

Om du vill dela filer mellan moduler i olika generationer får de inte innehålla några hemligheter, annars dekrypteras de inte.

## <a name="next-steps"></a>Nästa steg

Ytterligare ett exempel på åtkomst till värdlagring från en modul finns i Lagra data vid gränsen med [Azure Blob Storage på IoT Edge](how-to-store-data-blob.md).
