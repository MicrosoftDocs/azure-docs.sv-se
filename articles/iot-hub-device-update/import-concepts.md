---
title: Förstå enhets uppdatering för IoT Hub importera | Microsoft Docs
description: Viktiga begrepp för att importera en ny uppdatering till enhets uppdatering för IoT Hub.
author: andbrown
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: c6c6707a74007898c3cd73250709f4df3fabb37d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663979"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Importera uppdateringar till enhets uppdatering för IoT Hub
Innan du kan distribuera en uppdatering till enheter från enhets uppdateringen för IoT Hub måste du _Importera_ den uppdateringen till enhets uppdaterings tjänsten. Här följer en översikt över några viktiga begrepp att förstå när det gäller att importera uppdateringar.

## <a name="import-manifest"></a>Importera manifest

Ett import manifest är en JSON-fil som definierar viktig information om den uppdatering som du importerar. Du skickar både ditt import manifest och tillhör ande uppdaterings fil eller filer (till exempel ett uppdaterings paket för inbyggd program vara) som en del av import processen. De metadata som definieras i import manifestet används för att mata in uppdateringen. Vissa metadata används också vid distributions tiden, till exempel för att kontrol lera om en uppdatering har installerats korrekt.

Import manifestet innehåller flera objekt som representerar viktig enhets uppdatering för IoT Hub begrepp. Dessa begrepp beskrivs nedan.

### <a name="update-identity-update-id"></a>Uppdatera identitet (uppdaterings-ID)

Uppdaterings identiteten representerar den unika identifierare för en uppdatering. Den definierar viktiga egenskaper för en uppdatering som importeras. Uppdaterings identiteten består av tre delar:
* Provider: Detta är den enhet som skapar eller är direkt ansvarig för uppdateringen. Det är ofta ett företags namn.
* Namn: en identifierare för en klass med uppdateringar. Den här klassen kan vara allt du väljer. Det är ofta en enhet eller ett modell namn.
* Version: Detta är ett versions nummer som särskiljer den här uppdateringen från andra som har samma provider och namn. Den här versionen används av enhets uppdateringen för IoT Hub-tjänsten och kanske inte matchar en version av en enskild program varu komponent på enheten. 

### <a name="compatibility"></a>Kompatibilitet

För att förenkla uppdaterings distributioner kan enhets uppdatering för IoT Hub jämföra kompatibilitetsinställningar för en uppdatering, som definieras i import manifestet, med motsvarande enhets egenskaper. Endast uppdateringar som har matchande egenskaper kommer att returneras och vara tillgängliga för distribution.

### <a name="installedcriteria"></a>InstalledCriteria

InstalledCriteria används av uppdaterings agenten på en enhet för att avgöra om en uppdatering har installerats korrekt.


## <a name="next-steps"></a>Nästa steg

Om du är klar kan du prova med [guiden för att importera How-To](./import-update.md), som vägleder dig genom importen av steg för steg.


