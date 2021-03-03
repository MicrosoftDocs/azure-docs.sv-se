---
title: Azure Communication Services – vanliga frågor och svar om kända problem
description: Läs mer om Azure Communication Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e58335f1e266af651eb5867ca98e9ec979803b94
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655895"
---
# <a name="faq--known-issues"></a>FAQ/kända problem
Den här artikeln innehåller information om kända problem och vanliga frågor som rör Azure Communication Services.

## <a name="faq"></a>Vanliga frågor

### <a name="why-is-the-quality-of-my-video-degraded"></a>Varför är kvaliteten på min video försämrad?

Kvaliteten på video strömmar bestäms av storleken på klient sidans åter givning som användes för att initiera den data strömmen. När du prenumererar på en fjärrström kommer en mottagare att bestämma sin egen upplösning baserat på avsändarens åter givnings dimension på klient sidan.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Varför går det inte att räkna upp/välja MIC/talare-enheter i Safari?

Program kan inte räkna upp/välja MIC/talare-enheter (som Bluetooth) i Safari iOS/iPad. Det är en begränsning i operativ systemet – det finns alltid en enhet.

För Safari på MacOS – appen kan inte räkna upp/välja talare via kommunikations tjänster Enhetshanteraren – dessa måste väljas via operativ systemet. Om du använder Chrome på MacOS kan appen räkna upp/välja enheter via kommunikations tjänsterna Enhetshanteraren.

## <a name="known-issues"></a>Kända problem

Det här avsnittet innehåller information om kända problem som är associerade med Azure Communication Services.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Att växla video enheter flera gånger kan orsaka att uppspelning av video tillfälligt stoppas

Om du växlar mellan video enheter kan video strömmen pausas medan data strömmen hämtas från den valda enheten.

#### <a name="possible-causes"></a>Möjliga orsaker
Strömning från och växling mellan medie enheter är i beräknings intensiva. Att växla ofta kan leda till försämrade prestanda. Utvecklare uppmanas att stoppa en enhets ström innan en ny startas.
