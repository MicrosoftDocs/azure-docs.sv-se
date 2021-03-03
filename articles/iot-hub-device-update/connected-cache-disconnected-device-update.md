---
title: Förstå stöd för frånkopplad enhets uppdatering med Microsoft Connected cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Förstå stöd för frånkopplad enhets uppdatering med Microsoft Connected cache
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680058"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Förstå stöd för frånkopplade enhets uppdateringar

I ett scenario med transparent Gateway kan en eller flera enheter skicka sina meddelanden via en enda gateway-enhet som upprätthåller anslutningen till Azure IoT Hub. I dessa fall kanske de underordnade enheterna inte är anslutna till Internet eller inte kan ladda ned innehåll från Internet. I förhands gransknings IoT Edge för Microsoft Connected cache får du enhets uppdatering för Azure IoT Hub-kunder med en intelligent inbyggd cache, som möjliggör avbildnings-och paketbaserade uppdateringar av Linux OS-baserade enheter bakom och IoT Edge Gateway (underordnade IoT-enheter), och kommer också att spara bandbredd för enhets uppdatering för Azure IoT Hub-kunder.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Hur fungerar Microsoft Connected cache Preview för enhets uppdatering för Azure IoT Hub?

Microsoft Connected cache är ett intelligent, transparent cacheminne för innehåll som publicerats för enhets uppdatering för Azure IoT Hub-innehåll och som kan anpassas för att cachelagra innehåll från andra källor, till exempel paket arkiv. Microsoft Connected cache är ett kallt cacheminne som har värmts av klient begär Anden för de exakta fil intervall som begärs av leverans optimerings klienten och inte för att förcachelagra innehåll. I diagrammet och steg för steg-beskrivningen nedan förklaras hur Microsoft Connected cache fungerar i enhets uppdateringen för Azure IoT Hub-infrastrukturen.

>[!Note]
>Vid definiering av det här flödet har det förväntats att den IoT Edge gatewayen har Internet anslutning. För scenariot med en överordnad IoT Edge Gateway (kapslad gräns) kan "Content Delivery Network" (CDN) anses vara MCC som finns på den överordnade IoT Edge gatewayen.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Uppdatering av frånkopplad enhet" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft Connected cache distribueras som en IoT Edge-modul till lokal-servern.
2. Enhets uppdatering för Azure IoT Hub-klienter konfigureras för att ladda ned innehåll från Microsoft Connected cache genom att använda attributet GatewayHostName för enhets anslutnings strängen för IoT-löv enheter **eller** parent_hostname som angetts i config. toml för IoT Edge underordnade enheter.
3. Enhets uppdatering för Azure IoT Hub-klienter i båda fallen tar emot uppdaterings kommandon för hämtning av innehåll från enhets uppdateringen för Azure IoT Hub-tjänsten och begär uppdaterings innehåll till Microsoft Connected cache i stället för CDN. Microsoft Connected cache är som standard konfigurerat att lyssna på http-port 80 och leverans optimerings klienten gör innehålls förfrågan på port 80 så att den överordnade måste vara konfigurerad för att lyssna på den här porten.  Endast http-protokollet stöds för tillfället.
4. Microsoft Connected cache server laddar ned innehåll från CDN, dirigerar den lokala cachen som lagras på disk och levererar innehållet till enhets uppdateringen för Azure IoT Hub-klienten.
   
>[!Note]
>När du använder paketbaserade uppdateringar konfigureras Microsoft Connected cache-servern av administratören med det nödvändiga paketets värdnamn.

5. Efterföljande förfrågningar från andra enhets uppdateringar för Azure IoT Hub-klienter för samma uppdaterings innehåll kommer nu från cachen och Microsoft Connected cache kommer inte att göra förfrågningar till CDN för samma innehåll.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Stöd för industriella IoT (IIoT) med över-och underordnade värd scenarier

När en underordnad eller underordnad IoT Edge Gateway är värd för Microsoft-anslutna cache-server kommer den att konfigureras att begära uppdaterings innehåll från den överordnade IoT Edge gatewayen, som är värd för Microsoft Connected cache-servern. Detta krävs för så många nivåer som behövs innan det når den överordnade IoT Edge gateway som är värd för en Microsoft-ansluten cache-server som har Internet åtkomst. Från den Internet-anslutna servern begärs innehållet från CDN, där innehållet skickas tillbaka till den underordnade IoT Edge gateway som ursprungligen begärde innehållet. Innehållet lagras på alla nivåer på disken.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Åtkomst till Microsoft Connected cache Preview för enhets uppdatering för Azure IoT Hub

Microsoft Connected cache IoT Edge-modulen släpps som en för hands version för kunder som distribuerar lösningar med hjälp av enhets uppdatering för Azure IoT Hub. Åtkomst till förhands granskningen är efter inbjudan. [Begär åtkomst](https://aka.ms/MCCForDeviceUpdateForIoT) till för hands versionen av Microsoft Connected cache för enhets uppdatering för IoT Stäng och ange den information som krävs om du vill ha åtkomst till modulen.
