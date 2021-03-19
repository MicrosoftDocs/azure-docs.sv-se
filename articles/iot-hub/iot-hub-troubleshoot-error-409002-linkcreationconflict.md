---
title: Fel sökning av Azure IoT Hub-fel 409002 LinkCreationConflict
description: Förstå hur du åtgärdar fel 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 70b9be6fdb500d9f877659a12e6fdc0e206ea964
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92538229"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

I den här artikeln beskrivs orsaker och lösningar för **409002 LinkCreationConflict** -fel.

## <a name="symptoms"></a>Symtom

Du ser felet **409002 LinkCreationConflict** i loggar tillsammans med enhets från koppling eller meddelande om moln-till-enhet.

<!-- When using AMQP? -->

## <a name="cause"></a>Orsak

Detta fel inträffar vanligt vis när IoT Hub identifierar en klient har mer än en anslutning. När en ny anslutningsbegäran kommer till en enhet med en befintlig anslutning, avslutar IoT Hub den befintliga anslutningen med det här felet.

### <a name="cause-1"></a>Orsak 1

I det vanligaste fallet gör ett separat problem (till exempel [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) att enheten kopplas från. Enheten försöker återupprätta anslutningen direkt, men IoT Hub fortfarande anser att enheten är ansluten. IoT Hub stänger den tidigare anslutningen och loggar det här felet.

### <a name="cause-2"></a>Orsak 2

Felaktig logik på enhets sidan gör att enheten upprättar anslutningen när en redan är öppen.

## <a name="solution"></a>Lösning

Det här felet visas vanligt vis som en sido effekt av ett annat, tillfälligt problem. Leta efter andra fel i loggarna för att felsöka. Annars, se till att utfärda en ny anslutningsbegäran endast om anslutningen tappas.
