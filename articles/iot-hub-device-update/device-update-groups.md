---
title: Förstå enhets uppdatering för Azure IoT Hub enhets grupper | Microsoft Docs
description: Förstå hur enhets grupper används.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680010"
---
# <a name="device-groups"></a>Enhetsgrupper

En enhets grupp är en samling av enheter. Enhets grupper ger möjlighet att skala distributioner till många enheter. Varje enhet tillhör exakt en enhets grupp i taget.
Du kan välja att skapa flera enhets grupper för att ordna enheterna. Contoso kan till exempel använda enhets gruppen "flygning" för enheterna i testlabbet och enhets gruppen "utvärdering" för de enheter som dess fält team använder i Operations Center. Contoso kan också välja att gruppera sina produktions enheter baserat på deras geografiska regioner, så att de kan uppdatera enheter enligt ett schema som motsvarar deras regionala timezone. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Använda enhetens dubbla tagg för att skapa enhets grupper

Enhetens dubbla Taggar gör det möjligt för användare att gruppera enheter. Enheter måste ha en ADUGroup-nyckel och ett värde i enheten så att de kan grupperas.

### <a name="device-twin-tag-format"></a>Enhetens dubbla etikett format

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Okategoriserade enhets grupp

Okategoriserade är ett reserverat ord som används för att gruppera enheter:
- Har du inte ADUGroup-taggen enhets dubbla.
- Ha ADUGroup enhets dubbla taggar, men en grupp skapas inte med det här grupp namnet.

Överväg till exempel enheterna med enhetens dubbla Taggar nedan:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Nedan visas enheterna och de möjliga grupper som kan skapas för dem.

|Enhet |Group  |
|-----------|--------------|
|Device1    |Group1|
|Device2    |Group1|
|Device3    |Group2|
|Device4    |Ej kategoriserad|



## <a name="next-steps"></a>Nästa steg

[Skapa enhetsgrupp](./create-update-group.md)
