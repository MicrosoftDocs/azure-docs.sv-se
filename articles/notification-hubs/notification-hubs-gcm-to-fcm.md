---
title: Azure Notification Hubs och migrering av Google Firebase Cloud Messaging (FCM)
description: Beskriver hur Azure Notification Hubs riktar in Google-GCM till FCM-migreringen.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80127031"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Migrering av Azure Notification Hubs och Google Firebase Cloud Messaging

## <a name="current-state"></a>Nuvarande tillstånd

När Google tillkännagede migreringen från Google Cloud Messaging (GCM) till Firebase Cloud Messaging (FCM), push-tjänster som vårt behövde justera hur vi skickade meddelanden till Android-enheter för att kunna hantera ändringen.

Vi har uppdaterat vår tjänst Server del och publicerade uppdateringar till vår API och SDK vid behov. Med vår implementering har vi fattat beslutet att bibehålla kompatibilitet med befintliga GCM-aviserings scheman för att minimera kund påverkan. Det innebär att vi för närvarande skickar meddelanden till Android-enheter med FCM i FCM bakåtkompatibelt läge. Slutligen vill vi lägga till sant stöd för FCM, inklusive de nya funktionerna och nytto Last formatet. Det är en långsiktig ändring och den aktuella migreringen fokuserar på att upprätthålla kompatibilitet med befintliga program och SDK: er. Du kan använda antingen GCM-eller FCM-SDK: er i din app (tillsammans med vår SDK) och vi ser till att meddelandet skickas korrekt.

Vissa kunder har nyligen fått ett e-postmeddelande från Google-varning om appar med hjälp av en GCM-slutpunkt för aviseringar. Detta var bara en varning och inget har brutits – appens Android-meddelanden skickas fortfarande till Google för bearbetning och Google bearbetar dem fortfarande. Vissa kunder som har angett GCM-slutpunkten uttryckligen i sin tjänst konfiguration använde fortfarande den föråldrade slut punkten. Vi har redan identifierat denna lucka och arbetade med att åtgärda problemet när Google skickade e-postmeddelandet.

Vi har ersatt att den föråldrade slut punkten och korrigeringen distribueras.

## <a name="going-forward"></a>Gå framåt

Googles FCM FAQ säger att du inte behöver göra något. I [vanliga frågor och svar om FCM](https://developers.google.com/cloud-messaging/faq)kommer Google att "klient-SDK: er och GCM-token att fungera på obestämd tid. Du kommer dock inte att kunna rikta den senaste versionen av Google Play-tjänster i din Android-app om du inte migrerar till FCM. "

Om appen använder GCM-biblioteket kan du gå vidare och följa Googles instruktioner för att uppgradera till FCM-biblioteket i din app. Vår SDK är kompatibel med antingen, så du behöver inte uppdatera något i din app på vår sida (så länge du är uppdaterad med vår SDK-version).

## <a name="questions-and-answers"></a>Frågor och svar

Här är några svar på vanliga frågor som vi har hört från kunder:

**F:** Vad behöver jag göra för att vara kompatibel med slutdatumet (Googles aktuella slutdatum kan 29 och kan ändras)?

**A:** Alls. Vi upprätthåller kompatibiliteten med befintliga GCM-aviserings scheman. Din GCM-nyckel fortsätter att fungera som vanligt som alla GCM SDK: er och bibliotek som används av ditt program.

Om/när du bestämmer dig för att uppgradera till FCM-SDK: er och bibliotek för att dra nytta av nya funktioner kommer din GCM-nyckel fortfarande att fungera. Du kan växla till att använda en FCM-nyckel om du vill, men se till att du lägger till Firebase i ditt befintliga GCM-projekt när du skapar det nya Firebase-projektet. Detta garanterar bakåtkompatibilitet med dina kunder som kör äldre versioner av appen som fortfarande använder GCM-SDK: er och-bibliotek.

Om du skapar ett nytt FCM-projekt och inte kopplar till det befintliga GCM Notification Hubs-projektet kommer du att förlora möjligheten att skicka meddelanden till dina aktuella appar eftersom den nya FCM-nyckeln inte har någon länk till det gamla GCM-projektet.

**F:** Varför får jag det här e-postmeddelandet om gamla GCM-slutpunkter som används? Vad behöver jag göra?

**A:** Alls. Vi har migrerat till de nya slut punkterna och kommer att slutföras snart, så ingen ändring krävs. Ingenting är brutet, vår missade slut punkt orsakade bara varnings meddelanden från Google.

**F:** Hur kan jag övergå till de nya SDK: erna och biblioteken för FCM utan att behöva bryta befintliga användare?

S: uppgradering när som helst. Google har ännu inte lanserat någon utfasning av befintliga GCM-SDK: er och bibliotek. För att se till att du inte bryter push-meddelanden till dina befintliga användare, se till att när du skapar det nya Firebase-projektet som du associerar med ditt befintliga GCM-projekt. Detta säkerställer att nya Firebase hemligheter fungerar för användare som kör äldre versioner av din app med GCM-SDK: er och bibliotek, samt nya användare av din app med FCM-SDK: er och bibliotek.

**F:** När kan jag använda nya FCM-funktioner och scheman för mina meddelanden?

**A:** När vi publicerar en uppdatering till vår API och SDK: er är du kvar – vi förväntar dig att ha något för dig under de kommande månaderna.
