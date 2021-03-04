---
title: Enhets uppdatering för IoT Hub uppdaterings manifest | Microsoft Docs
description: Lär dig hur egenskaper skickas från enhets uppdaterings tjänsten till enheten under en uppdatering
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030689"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Enhets uppdatering för IoT Hub uppdaterings manifest

## <a name="overview"></a>Översikt

Enhets uppdatering för IoT Hub använder ett _uppdaterings manifest_ för att kommunicera åtgärder och även metadata som stöder dessa åtgärder via gränssnitts egenskaperna [AzureDeviceUpdateCore. OrchestratorMetadata: 4](./device-update-plug-and-play.md).
Det här dokumentet beskriver grunderna för hur `updateManifest` egenskapen i- `AzureDeviceUpdateCore.OrchestratorMetadata:4` gränssnittet används av enhets uppdaterings agenten. `AzureDeviceUpdateCore.OrchestratorMetadata:4`Gränssnitts egenskaperna skickas från enhets uppdateringen för IoT Hub tjänst till enhets uppdaterings agenten. `updateManifest`Är ett serialiserat JSON-objekt som parsas av enhets uppdaterings agenten.

### <a name="an-example-update-manifest"></a>Ett exempel på ett uppdaterings manifest

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

Syftet med uppdaterings manifestet är att beskriva innehållet i en uppdatering, nämligen dess identitet, typ, installerade kriterier och metadata för uppdatering av filen. Dessutom signeras uppdaterings manifestet kryptografiskt för att tillåta att enhets uppdaterings agenten verifierar sin äkthet. Mer information om hur uppdaterings manifestet används för att importera innehåll på ett säkert sätt finns i säkerhets dokumentationen för [enhets uppdateringar](./device-update-security.md) .

## <a name="import-manifest-vs-update-manifest"></a>Importera manifest vs Update-Manifest

Det är viktigt att förstå skillnaderna mellan import manifestet och uppdaterings Manifestets koncept i enhets uppdatering för IoT Hub. 
* [Import manifestet](./import-concepts.md) skapas av vem som skapar motsvarande uppdatering. Den beskriver innehållet i den uppdatering som ska importeras till enhets uppdatering för IoT Hub. 
* Uppdaterings manifestet genereras automatiskt av enhets uppdateringen för IoT Hub tjänst, med några av de egenskaper som definierades i import manifestet. Den används för att förmedla relevant information till enhets uppdaterings agenten under uppdaterings processen. 

Varje manifest typ har sin egen schema-och schema version.

## <a name="update-manifest-properties"></a>Uppdatera manifest egenskaper

Du hittar de övergripande definitionerna av egenskaperna för uppdaterings manifestet i de gränssnitts definitioner som finns [här](./device-update-plug-and-play.md). För att ge djupare kontext tar vi en närmare titt på egenskaperna och hur de används i systemet.

### <a name="updateid"></a>updateId

Innehåller `provider` , `name` och `version` , som representerar den exakta enhets uppdateringen för IoT Hub uppdaterings identitet som används för att fastställa kompatibla enheter för uppdateringen.

### <a name="updatetype"></a>Uppdateringstyp

Representerar den typ av uppdatering som hanteras av en speciell typ av uppdaterings hanterare. Det följer formatet `microsoft/swupdate:1` för en avbildningsbaserad uppdatering och `microsoft/apt:1` för en paket-baserad uppdatering (se `Update Handler Types` avsnittet nedan).

### <a name="installedcriteria"></a>installedCriteria

En sträng som innehåller information som krävs av enhets uppdaterings agentens uppdaterings hanterare för att avgöra om uppdateringen är installerad på enheten. I `Update Handler Types` avsnittet dokumenteras formatet för `installedCriteria` , för varje uppdaterings typ som stöds av enhets uppdatering för IoT Hub.

### <a name="files"></a>filer

Talar om för enhets uppdaterings agenten vilka filer som ska laddas ned och vilken hash som ska användas för att kontrol lera att filerna hämtades korrekt.
Här är en närmare titt på `files` egenskaps innehållet:

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

Utanför `updateManifest` är `fileUrls` matrisen med JSON-objekt.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

Både `FILE_ID_STRING` , inom `fileUrls` och `files` är desamma (till exempel "0000" i `files` har URL: en i "0000" i `fileUrls` ).

### <a name="manifestversion"></a>manifestVersion

En sträng som representerar schema versionen.

## <a name="update-handler-types"></a>Typer av uppdaterings hanterare

|Uppdaterings metod|Typ av uppdaterings hanterare|Uppdaterings typ|Installerade villkor|Förväntade filer för publicering|
|-------------|-------------------|----------|-----------------|--------------|
|Bildbaserad|SWUpdate|"Microsoft/swupdate: version"|Referens bilden sparar tipset för dess version i/etc/ADU-version-filen.  |. SWU-fil som innehåller SWUpdate-avbildning|
|Package-based|APT|"Microsoft/apt: version"|`<name>` + "-" + `<version>` (definierade egenskaper i manifest filen för apt|`<APT Update Manifest>`. JSON som innehåller APT-konfigurationen och paket listan|

