---
title: Azure Communication Services – kända problem
description: Läs om kända problem med Azure Communication Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628162"
---
# <a name="known-issues-azure-communication-services"></a>Kända problem: Azure Communication Services

Den här artikeln innehåller information om kända problem som är associerade med Azure Communication Services.

## <a name="video-streaming-quality-on-chromeandroid"></a>Kvalitet på video strömning på Chrome/Android 

Prestanda för video strömning kan försämras på Chrome Android.

### <a name="possible-causes"></a>Möjliga orsaker
Fjärrströmmarnas kvalitet beror på upplösningen på klient sidans åter givning som användes för att initiera den data strömmen. När du prenumererar på en fjärrström kommer en mottagare att bestämma sin egen upplösning baserat på avsändarens åter givnings dimension på klient sidan.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Mikrofoner för Bluetooth-headset identifieras inte

Det kan uppstå problem när du ansluter ditt Bluetooth-headset till ett kommunikations tjänst samtal.

### <a name="possible-causes"></a>Möjliga orsaker
Det finns inget alternativ för att välja Bluetooth-mikrofon på iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Att växla video enheter flera gånger kan orsaka att uppspelning av video tillfälligt stoppas

Om du växlar mellan video enheter kan video strömmen pausas medan data strömmen hämtas från den valda enheten.

### <a name="possible-causes"></a>Möjliga orsaker
Strömning från och växling mellan medie enheter är i beräknings intensiva. Att växla ofta kan leda till försämrade prestanda. Utvecklare uppmanas att stoppa en enhets ström innan en ny startas.
