---
title: Metod tips för enhets utveckling i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver metod tips för enhets anslutning i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054952"
---
# <a name="best-practices-for-device-development"></a>Metod tips för utveckling av enheter

*Den här artikeln gäller för enhets utvecklare.*

Dessa rekommendationer visar hur du implementerar enheter för att dra nytta av den inbyggda haveri återställningen och automatisk skalning i IoT Central.

I följande lista visas det höga nivå flödet när en enhet ansluter till IoT Central:

1. Använd DPS för att etablera enheten och hämta en enhets anslutnings sträng.

1. Använd anslutnings strängen för att ansluta IoT Central interna IoT Hub-slutpunkten. Skicka data till och ta emot data från IoT Central programmet.

1. Om enheten får anslutnings fel, beroende på vilken typ av fel som används, kan du antingen försöka ansluta igen eller reetablera enheten.

## <a name="use-dps-to-provision-the-device"></a>Använd DPS för att etablera enheten

Om du vill etablera en enhet med DPS använder du omfångs-ID, autentiseringsuppgifter och enhets-ID från IoT Central programmet. Mer information om typer av autentiseringsuppgifter finns i [grupp registrering för X. 509](concepts-get-connected.md#x509-group-enrollment) och SAS- [gruppregistrering](concepts-get-connected.md#sas-group-enrollment). Mer information om enhets-ID: n finns i [enhets registrering](concepts-get-connected.md#device-registration).

Vid lyckad returnerar DPS en anslutnings sträng som enheten kan använda för att ansluta till ditt IoT Central-program. Information om hur du felsöker etablerings fel finns i [kontrol lera enhetens etablerings status](troubleshoot-connection.md#check-the-provisioning-status-of-your-device).

Enheten kan cachelagra anslutnings strängen som ska användas för senare anslutningar. Enheten måste dock vara beredd för att [Hantera anslutnings problem](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Anslut till IoT Central

Använd anslutnings strängen för att ansluta IoT Central interna IoT Hub-slutpunkten. Anslutningen gör att du kan skicka telemetri till ditt IoT Central program, synkronisera egenskaps värden med ditt IoT Central-program och svara på kommandon som skickas av ditt IoT Central program.

## <a name="handle-connection-failures"></a>Hantera anslutnings problem

Vid skalning eller haveri beredskap kan IoT Central uppdatera sin underliggande IoT-hubb. För att upprätthålla anslutningen bör enhets koden hantera vissa anslutnings fel genom att upprätta en anslutning till den nya IoT Hub slut punkten.

Om enheten får något av följande fel när den ansluter, ska den göra om etablerings steget med DPS för att få en ny anslutnings sträng. Felen innebär att anslutnings strängen som enheten använder inte längre är giltig:

- Det går inte att komma åt IoT Hub slut punkten.
- Förfallen säkerhetstoken.
- Enheten är inaktive rad i IoT Hub.

Om enheten får något av följande fel när den ansluter bör den använda en annan strategi för att försöka ansluta igen. De här felen innebär att den anslutnings sträng som enheten använder fortfarande är giltig, men för tillfälliga förhållanden hindrar enheten från att ansluta:

- Operatören blockerade enheten.
- Internt fel 500 från tjänsten.

Mer information om enhets fel koder finns i [Felsöka enhets anslutningar](troubleshoot-connection.md).

## <a name="next-steps"></a>Nästa steg

Om du är enhets utvecklare är några förslag på nästa steg att:

- Granska en exempel kod som visar hur du använder SAS-token i [Självstudier: skapa och ansluta ett klient program till ditt Azure IoT Central-program](tutorial-connect-device.md)
- Lär dig hur du [ansluter enheter med X. 509-certifikat med hjälp av Node.js Device SDK för IoT Central program](how-to-connect-devices-x509.md)
- Lär dig hur du [övervakar enhets anslutningar med hjälp av Azure CLI](./howto-monitor-devices-azure-cli.md)
- Lär dig hur du [definierar en ny IoT-enhets typ i ditt Azure IoT Central-program](./howto-set-up-template.md)
- Läs om [Azure IoT Edge enheter och Azure IoT Central](./concepts-iot-edge.md)
