---
title: Importera uppdateringar till enhets uppdatering för IoT Hub-schema och annan information | Microsoft Docs
description: Schema och annan relaterad information (inklusive objekt) som används när du importerar uppdateringar till enhets uppdatering för IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102055011"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Importera uppdateringar till enhets uppdatering för IoT Hub-schema och annan information
Om du vill importera en uppdatering till enhets uppdateringen för IoT Hub, se till att du har granskat [begreppen](import-concepts.md) och [instruktions guiden](import-update.md) först. Om du är intresse rad av informationen om det schema som används för att skapa ett import manifest, samt information om relaterade objekt, se nedan.

## <a name="import-manifest-schema"></a>Importera manifest schema

| Namn | Typ | Beskrivning | Begränsningar |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` jobbobjektet | Uppdatera identitet. |
| Uppdateringstyp | sträng | Uppdateringstyp: <br/><br/> * Ange `microsoft/apt:1` när en Package-baserad uppdatering ska utföras med hjälp av referens agent.<br/> * Ange `microsoft/swupdate:1` när du utför en avbildningsbaserad uppdatering med hjälp av referens agent.<br/> * Ange `microsoft/simulator:1` när du använder exempel agent Simulator.<br/> * Ange en anpassad typ om du utvecklar en anpassad agent. | Format: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Maximalt 32 tecken totalt |
| InstalledCriteria | sträng | Sträng som tolkas av agenten för att avgöra om uppdateringen har genomförts:  <br/> * Ange **värdet** för SWVersion för uppdaterings typen `microsoft/swupdate:1` .<br/> * Ange `{name}-{version}` för uppdaterings typ `microsoft/apt:1` , vars namn och version hämtas från apt-filen.<br/> * Ange hash för uppdaterings filen för uppdaterings typ `microsoft/simulator:1` .<br/> * Ange en anpassad sträng om du utvecklar en anpassad agent.<br/> | Högst 64 tecken |
| Kompatibilitet | Objekt mat ris `CompatibilityInfo` | Kompatibilitetsinformation för enhet som är kompatibel med den här uppdateringen. | Högst 10 objekt |
| CreatedDateTime | datum/tid | Datum och tid då uppdateringen skapades. | Avgränsat ISO 8601 datum-och tids format, i UTC |
| ManifestVersion | sträng | Importera manifest schema version. Ange `2.0` , som är kompatibel med `urn:azureiot:AzureDeviceUpdateCore:1` gränssnittet och `urn:azureiot:AzureDeviceUpdateCore:4` gränssnittet. | Måste vara `2.0` |
| Filer | Objekt mat ris `File` | Uppdatera nytto Last filer | Högst 5 filer |

## <a name="updateid-object"></a>UpdateId-objekt

| Namn | Typ | Beskrivning | Begränsningar |
| --------- | --------- | --------- | --------- |
| Leverantör | sträng | Leverantörs del av uppdaterings identiteten. | 1-64 tecken, alfanumeriskt, prick och tank streck. |
| Name | sträng | Namn på uppdaterings identiteten. | 1-64 tecken, alfanumeriskt, prick och tank streck. |
| Version | version | Versions del av uppdaterings identiteten. | 2 till 4 delar, punktavgränsat versions nummer mellan 0 och 2147483647. Inledande nollor tas bort. |

## <a name="file-object"></a>Fil objekt

| Namn | Typ | Beskrivning | Begränsningar |
| --------- | --------- | --------- | --------- |
| Sökväg | sträng | Filens namn | Måste vara unikt inom en uppdatering |
| SizeInBytes | Int64 | Storleken på filen i byte. | Högst 800 MB per enskild fil eller 800 MB gemensamt per uppdatering |
| Hash | `Hashes` jobbobjektet | JSON-objekt som innehåller hash (ES) av filen |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo-objekt

| Namn | Typ | Beskrivning | Begränsningar |
| --- | --- | --- | --- |
| DeviceManufacturer | sträng | Enhetens tillverkare som uppdateringen är kompatibel med. | 1-64 tecken, alfanumeriskt, prick och tank streck. |
| DeviceModel | sträng | Enhets modellen som uppdateringen är kompatibel med. | 1-64 tecken, alfanumeriskt, prick och tank streck. |

## <a name="hashes-object"></a>Hash-objekt

| Name | Krävs | Typ | Beskrivning |
| --------- | --------- | --------- | --------- |
| SHA256 | True | sträng | Base64-kodad hash för filen med SHA-256-algoritmen. |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att [Importera begrepp](./import-concepts.md).

Om du är klar kan du prova med [guiden för att importera How-To](./import-update.md), som vägleder dig genom importen av steg för steg.
