---
title: Fel sökning av Azure IoT Hub-fel 400027 ConnectionForcefullyClosedOnNewConnection
description: Förstå hur du åtgärdar fel 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061374"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

I den här artikeln beskrivs orsaker och lösningar för **400027 ConnectionForcefullyClosedOnNewConnection** -fel.

## <a name="symptoms"></a>Symtom

Enheten är frånkopplad med **Communication_Error** som **ConnectionStatusChangeReason** med hjälp av transport typen .NET SDK och MQTT.

Din enhets-till-moln-dubbla åtgärd (till exempel Read eller patch rapporterade egenskaper) eller direkt metod anrop Miss lyckas med felkoden **400027**.

## <a name="cause"></a>Orsak

En annan klient skapade en ny anslutning till IoT Hub med samma autentiseringsuppgifter, så IoT Hub stängde den tidigare anslutningen. IoT Hub tillåter inte att fler än en klient ansluter med samma uppsättning autentiseringsuppgifter.

## <a name="solution"></a>Lösning

Se till att varje klient ansluter till IoT Hub med sin egen identitet.