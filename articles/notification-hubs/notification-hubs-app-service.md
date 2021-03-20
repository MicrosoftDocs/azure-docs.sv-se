---
title: Notification Hubs integration med App Service Mobile Apps
description: Lär dig hur Azure Notification Hubs fungerar med Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88004065"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrering med Mobilappar i Apptjänst

För att under lätta en sömlös och enhetlig upplevelse i Azure-tjänster har [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) inbyggt stöd för meddelanden med Azure Notification Hubs. [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) erbjuder en mycket skalbar, globalt tillgänglig mobil program utvecklings plattform för företags utvecklare och system integrerare som ger en omfattande uppsättning funktioner för mobila utvecklare.

Mobile Apps utvecklare kan använda Notification Hubs med följande arbets flöde:

1. Hämta enhetens PNS-referens.
2. Registrera enheten med Notification Hubs att Mobile Apps använda API: er för klient-SDK-registrering.

    > [!NOTE]
    > Observera att Mobile Apps av säkerhetsskäl tar bort alla taggar vid registreringen. Arbeta med Notification Hubs direkt från din serverdel för att associera taggar med enheter.

3. Skicka meddelanden från appens Server del med Notification Hubs.

Vissa fördelar den här integrationen ger:

- **Mobile Apps klient-SDK**: er: dessa SDK: er för flera plattformar innehåller API: er för registrering och kommunikation med den meddelande hubb som är länkad till mobilappen. Du behöver inte Notification Hubs autentiseringsuppgifter eller arbeta med ytterligare en tjänst.
  - *Push till användare*: SDK: n taggar automatiskt den angivna enheten med ett Mobile Apps autentiserat användar-ID för att aktivera scenariot "Skicka till användare".
  - *Push till enhet*: SDK: erna använder automatiskt Mobile Apps installations-ID som en GUID att registrera med Notification Hubs, så det finns inga behov av att underhålla flera tjänst-GUID.
- **Installations modell**: Mobile Apps fungerar med den Notification Hubs senaste push-modellen för att representera alla push-egenskaper som är kopplade till en enhet i en JSON-installation som överensstämmer med push-Notification Services och är enkel att använda.
- **Flexibilitet**: Utvecklare kan alltid välja att arbeta direkt med Notification Hubs, även om integrationen redan är på plats.
- **Integrerad upplevelse i [Azure Portal](https://portal.azure.com)**: push som en funktion representeras visuellt i Mobile Apps och utvecklare kan enkelt arbeta med den associerade Notification Hub via Mobile Apps.
