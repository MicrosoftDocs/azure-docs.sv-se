---
title: Förstå stödet för frånkopplad enhetsuppdatering med Microsoft Ansluten cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Förstå stödet för frånkopplad enhetsuppdatering med Microsoft Ansluten cache
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e216d42ff1f279d87e657126514fcfb50960f806
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811912"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Förstå stödet för uppdateringar av frånkopplade enheter

I ett scenario med transparent gateway kan en eller flera enheter skicka sina meddelanden via en enda gatewayenhet som upprätthåller anslutningen till Azure IoT Hub. I dessa fall kanske de underordnade enheterna inte har någon Internetanslutning eller så kanske de inte kan ladda ned innehåll från Internet. Modulen Microsoft Ansluten cache Preview IoT Edge tillhandahåller enhetsuppdatering för Azure IoT Hub-kunder med funktioner för en intelligent cache i nätverket, som möjliggör avbildningsbaserade och paketbaserade uppdateringar av Linux OS-baserade enheter bakom och IoT Edge-gateway (underordnade IoT-enheter) och hjälper även till att spara bandbredd för enhetsuppdatering för Azure IoT Hub-kunder.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Hur fungerar Microsoft Ansluten cache förhandsversion för enhetsuppdatering för Azure IoT Hub?

Microsoft Ansluten cache Preview är en intelligent, transparent cache för innehåll som publicerats för enhetsuppdatering för Azure IoT Hub-innehåll och kan även anpassas för att cachelagra innehåll från andra källor som paketlagringsplatsen. Microsoft Ansluten cache är en kall cache som värmes av klientbegäranden för de exakta filintervall som begärs av Leveransoptimering-klienten och som inte förinstallerar innehåll. Diagrammet och den stegvisa beskrivningen nedan förklarar hur Microsoft Ansluten cache fungerar i enhetsuppdateringen för Azure IoT Hub infrastruktur.

>[!Note]
>När du definierar det här flödet har det förutsättts att IoT Edge gateway har Internetanslutning. I scenariot med den underordnade IoT Edge-gatewayen (kapslad edge) kan "Content Delivery Network" (CDN) betraktas som den MCC som finns på den överordnade IoT Edge gatewayen.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Uppdatering av frånkopplad enhet" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft Ansluten cache distribueras som en IoT Edge-modul till den lokala servern.
2. Enhetsuppdatering för Azure IoT Hub-klienter konfigureras för att ladda ned innehåll från Microsoft Ansluten cache enligt attributet GatewayHostName i enhetsanslutningssträngen för IoT-lövenheter **ELLER** parent_hostname som angetts i config.toml för IoT Edge underordnade enheter.
3. Enhetsuppdatering för Azure IoT Hub klienter tar i båda fallen emot kommandon för nedladdning av uppdateringsinnehåll från enhetsuppdateringen för Azure IoT Hub-tjänsten och begär uppdateringsinnehåll till Microsoft Ansluten cache i stället för CDN. Microsoft Ansluten cache är som standard konfigurerat för att lyssna på http-port 80 och Leveransoptimering-klienten skickar innehållsbegäran på port 80 så att den överordnade måste konfigureras för att lyssna på den här porten.  Endast http-protokollet stöds för närvarande.
4. Microsoft Ansluten cache-servern laddar ned innehåll från CDN, överför sin lokala cache som lagras på disken och levererar innehållet till enhetsuppdateringen för Azure IoT Hub klient.
   
>[!Note]
>När du använder paketbaserade uppdateringar konfigureras Microsoft Ansluten cache-servern av administratören med det paketvärdnamn som krävs.

5. Efterföljande begäranden från andra enhetsuppdateringsklienter för Azure IoT Hub-klienter för samma uppdateringsinnehåll kommer nu från cachen och Microsoft Ansluten cache kommer inte att göra begäranden till CDN för samma innehåll.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Stöd för bransch-IoT (IIoT) med överordnade/underordnade värdscenarier

När en underordnad eller underordnad IoT Edge-gateway är värd för Microsoft Ansluten cache-servern konfigureras den för att begära uppdateringsinnehåll från den överordnade IoT Edge-gatewayen, som är värd för Microsoft Ansluten cache-servern. Detta krävs för så många nivåer som behövs innan den överordnade gatewayen IoT Edge värd för en Microsoft Ansluten cache-server som har Internetåtkomst. Från den Internetanslutna servern begärs innehållet från CDN där innehållet levereras tillbaka till den underordnade IoT Edge som ursprungligen begärde innehållet. Innehållet lagras på disk på varje nivå.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Åtkomst till Microsoft Ansluten cache förhandsgranskning för enhetsuppdatering för Azure IoT Hub

Microsoft Ansluten cache IoT Edge-modulen släpps som en förhandsversion för kunder som distribuerar lösningar med enhetsuppdatering för Azure IoT Hub. Åtkomst till förhandsversionen är via inbjudan. [Begär åtkomst](https://aka.ms/MCCForDeviceUpdateForIoT) till Microsoft Ansluten cache Preview för enhetsuppdatering för IoT Firmware och ange den information som efterfrågas om du vill ha åtkomst till modulen.
