---
title: Nyheter i Azure Defender för IoT
description: I den här artikeln kan du se vad som är nytt i den senaste versionen av Defender för IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2021
ms.author: shhazam
ms.openlocfilehash: 0281ebcdf1da27513e9b9256b508d911ec7697b5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937725"
---
# <a name="whats-new"></a>Nyheter

Defender för IoT 10,0 finns funktions förbättringar som förbättrar säkerheten, hanteringen och användbarhet.

## <a name="security"></a>Säkerhet

Förbättringar av certifikat-och lösen ords återställning har gjorts för den här versionen.

### <a name="certificates"></a>Certifikat
  
Med den här versionen kan du:

- Ladda upp SSL-certifikat direkt till sensorer och lokala hanterings konsoler.
- Utför verifiering mellan den lokala hanterings konsolen och anslutna sensorer, och mellan en hanterings konsol och en hanterings konsol med hög tillgänglighet. Verifieringen baseras på förfallo datum, rot certifikat utfärdare och listor över återkallade certifikat.  Om verifieringen Miss lyckas kommer sessionen inte att fortsätta.

För uppgraderingar:

- Det finns ingen ändring i SSL-certifikatet eller verifierings funktionen under uppgraderingen.
- Efter uppgraderingen kan de administrativa användarna av sensorer och den lokala hanterings konsolen ersätta SSL-certifikat eller aktivera verifiering av SSL-certifikat från fönstret system inställningar, SSL-certifikat.  

För nya installationer:

- Under inloggning första gången måste användare antingen använda ett SSL-certifikat (rekommenderas) eller ett lokalt genererat självsignerat certifikat (rekommenderas inte)
- Certifikat validering är aktiverat som standard för nya installationer.

### <a name="password-recovery"></a>Lösen ords återställning
  
De administrativa användarna av sensorer och den lokala hanterings konsolen kan nu återställa lösen ord från Azure Defender för IoT-portalen. Tidigare lösen ords återställning krävde åtgärder från support teamet.

## <a name="onboarding"></a>Publicering

### <a name="on-premises-management-console---committed-devices"></a>Lokal hanterings konsol-allokerade enheter

Efter första inloggning i den lokala hanterings konsolen måste användarna nu ladda upp en aktiverings fil. Filen innehåller det sammanlagda antalet enheter som ska övervakas i organisationens nätverk. Det här antalet kallas för antalet allokerade enheter.
Allokerade enheter definieras under onboarding-processen på Azure Defender för IoT-portalen, där aktiverings filen skapas.
Första gången användare och användare uppgraderar krävs för att överföra aktiverings filen.
Efter den första aktiveringen kan antalet enheter som har identifierats i nätverket överskrida antalet allokerade enheter. Den här händelsen kan inträffa, till exempel om du ansluter fler sensorer till hanterings konsolen. Om det uppstår en avvikelse mellan antalet identifierade enheter och antalet allokerade enheter visas en varning i hanterings konsolen. Om den här händelsen inträffar bör du ladda upp en ny aktiverings fil.

### <a name="pricing-page-options"></a>Alternativ för pris Sidan

På sidan prissättning kan du publicera nya prenumerationer på Azure Defender för IoT och definiera allokerade enheter i nätverket.  
På sidan prissättning kan du dessutom hantera befintliga prenumerationer som är kopplade till en sensor och uppdatera enhets åtagandet.

### <a name="view-and-manage-onboarded-sensors"></a>Visa och hantera inbyggda sensorer

På en ny Portal sida för webbplatser och sensorer kan du:

- Lägg till beskrivande information om sensorn. Till exempel en zon som är associerad med sensorn, eller Taggar med fri text.
- Visa och filtrera sensor information. Du kan till exempel Visa information om sensorer som är anslutna till molnet eller lokalt hanterade eller Visa information om sensorer i en speciell zon.  

## <a name="usability"></a>Användbarhet

### <a name="azure-sentinel-new-connector-page"></a>Sidan Azure Sentinel New Connector

Sidan Azure Defender för IoT data Connector i Azure Sentinel har återskapats. Data kopplingen baseras nu på prenumerationer snarare än IoT-hubbar. gör det möjligt för kunderna att hantera sin konfigurations anslutning till Azure Sentinel.

### <a name="azure-portal-permission-updates"></a>Azure Portal behörighets uppdateringar  

Security Reader och Security Administrator support har lagts till.

## <a name="other-updates"></a>Övriga uppdateringar

### <a name="access-group---zone-permissions"></a>Åtkomst grupp – zon behörigheter
  
Åtkomst grupps reglerna för den lokala hanterings konsolen innehåller inte alternativet att bevilja åtkomst till en speciell zon. Det finns ingen ändring i Definiera regler som använder platser, regioner och affär senheter.   Efter uppgraderingen kommer åtkomst grupper som innehåller regler som tillåter åtkomst till vissa zoner att ändras för att tillåta åtkomst till dess överordnade plats, inklusive alla dess zoner.

### <a name="terminology-changes"></a>Ändringar i terminologi

Termen till gång har bytt namn till enheten i sensorn och den lokala hanterings konsolen, rapporter och andra lösnings gränssnitt.
I sensor-och lokal hanterings konsol aviseringar har termen hantera den här händelsen fått reparations steg.

## <a name="next-steps"></a>Nästa steg

[Komma igång med Defender för IoT](getting-started.md)
