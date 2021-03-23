---
title: Nyheter i Azure Defender för IoT
description: I den här artikeln kan du se vad som är nytt i den senaste versionen av Defender för IoT.
ms.topic: how-to
ms.date: 03/14/2021
ms.openlocfilehash: 970d46a76f125dcaf82491fdea877e6874e2384f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778703"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Vad är nytt i Azure Defender för IoT?

Den här artikeln innehåller nya funktioner och funktions förbättringar för Defender för IoT.

Antecknade funktioner finns i för hands version. I [tilläggs villkoren för Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ingår ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.
## <a name="march-2021"></a>Mars 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Sensor-förbättrade anpassade aviserings regler (offentlig för hands version)

Nu kan du skapa anpassade varnings regler baserat på den dag, den grupp av dagar och den tids period som nätverks aktiviteten har identifierats.  Att arbeta med villkor för dag och tid är användbart, till exempel i fall där allvarlighets graden för aviseringar härleds vid tidpunkten för att aviserings händelsen ska äga rum. Du kan till exempel skapa en anpassad regel som utlöser en avisering med hög allvarlighets grad när en nätverks aktivitet identifieras på en helg eller på kvällen.

Den här funktionen är tillgänglig i sensorn med version 10,2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Lokal hanterings konsol-exportera aviseringar (offentlig för hands version)

Aviserings informationen kan nu exporteras till en. csv-fil från den lokala hanterings konsolen. Du kan exportera information om alla aviseringar som identifierats eller exportera information baserat på den filtrerade vyn.

Den här funktionen är tillgänglig i den lokala hanterings konsolen med version 10,2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Lägg till ett andra nätverks gränssnitt i den lokala hanterings konsolen (offentlig för hands version)

Du kan nu förbättra säkerheten för distributionen genom att lägga till ett andra nätverks gränssnitt i den lokala hanterings konsolen. Den här funktionen gör det möjligt för din lokala hantering att ha den anslutna sensorer i ett säkert nätverk, samtidigt som användarna kan komma åt den lokala hanterings konsolen via ett andra separat nätverks gränssnitt.

Den här funktionen är tillgänglig i den lokala hanterings konsolen med version 10,2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Device Builder – ny Micro agent (offentlig för hands version)

Det finns en ny Device Builder-modul. Modulen, som kallas Micro-agent, tillåter:

- **Integrering med azure IoT Hub och Azure Defender för IoT** – utveckla starkare slut punkts säkerhet direkt till dina IoT-enheter genom att integrera den med det övervaknings alternativ som tillhandahålls av både Azure IoT Hub och Azure Defender för IoT.
- **Flexibla distributions alternativ med stöd för vanliga IoT-operativsystem** – kan distribueras antingen som ett binärt paket eller som ändrings bara käll kod, med stöd för vanliga IoT-operativsystem som Linux och Azure återställnings tider.
- **Lägsta resurs krav utan några OS-kernel-beroenden** – liten storlek, låg processor förbrukning och inga OS-kernel-beroenden.
- **Hantering av säkerhets position** – proaktivt övervaka säkerhets position för dina IoT-enheter.
- **Kontinuerlig identifiering av IoT/nätverks-hot i real tid** – identifiera hot som botnät, brutna Force-försök, krypto gruvprogram och misstänkt nätverks aktivitet

Den föråldrade versionen av Defender-IoT-Micro-Micro-agenten kommer att flyttas till den *agentbaserade lösningen för enhets byggare>klassisk* mapp.

Den här funktionen är tillgänglig med den aktuella offentliga för hands versionen av molnet.

## <a name="january-2021"></a>Januari 2021

- [Säkerhet](#security)
- [Registrering](#onboarding)
- [Användbarhet](#usability)
- [Övriga uppdateringar](#other-updates)
### <a name="security"></a>Säkerhet

Förbättringar av certifikat-och lösen ords återställning har gjorts för den här versionen.

#### <a name="certificates"></a>Certifikat
  
Med den här versionen kan du:

- Ladda upp SSL-certifikat direkt till sensorer och lokala hanterings konsoler.
- Utför verifiering mellan den lokala hanterings konsolen och anslutna sensorer, och mellan en hanterings konsol och en hanterings konsol med hög tillgänglighet. Verifieringen baseras på förfallo datum, rot certifikat utfärdare och listor över återkallade certifikat.  Om verifieringen Miss lyckas kommer sessionen inte att fortsätta.

För uppgraderingar:

- Det finns ingen ändring i SSL-certifikatet eller verifierings funktionen under uppgraderingen.
- Efter uppgraderingen kan de administrativa användarna av sensorer och den lokala hanterings konsolen ersätta SSL-certifikat eller aktivera verifiering av SSL-certifikat från fönstret system inställningar, SSL-certifikat.  

För nya installationer:

- Under inloggning första gången måste användare antingen använda ett SSL-certifikat (rekommenderas) eller ett lokalt genererat självsignerat certifikat (rekommenderas inte)
- Certifikat validering är aktiverat som standard för nya installationer.

#### <a name="password-recovery"></a>Lösen ords återställning
  
De administrativa användarna av sensorer och den lokala hanterings konsolen kan nu återställa lösen ord från Azure Defender för IoT-portalen. Tidigare lösen ords återställning krävde åtgärder från support teamet.

### <a name="onboarding"></a>Publicering

#### <a name="on-premises-management-console---committed-devices"></a>Lokal hanterings konsol-allokerade enheter

Efter första inloggning i den lokala hanterings konsolen måste användarna nu ladda upp en aktiverings fil. Filen innehåller det sammanlagda antalet enheter som ska övervakas i organisationens nätverk. Det här antalet kallas för antalet allokerade enheter.
Allokerade enheter definieras under onboarding-processen på Azure Defender för IoT-portalen, där aktiverings filen skapas.
Första gången användare och användare uppgraderar krävs för att överföra aktiverings filen.
Efter den första aktiveringen kan antalet enheter som har identifierats i nätverket överskrida antalet allokerade enheter. Den här händelsen kan inträffa, till exempel om du ansluter fler sensorer till hanterings konsolen. Om det uppstår en avvikelse mellan antalet identifierade enheter och antalet allokerade enheter visas en varning i hanterings konsolen. Om den här händelsen inträffar bör du ladda upp en ny aktiverings fil.

#### <a name="pricing-page-options"></a>Alternativ för pris Sidan

På sidan prissättning kan du publicera nya prenumerationer på Azure Defender för IoT och definiera allokerade enheter i nätverket.  
På sidan prissättning kan du dessutom hantera befintliga prenumerationer som är kopplade till en sensor och uppdatera enhets åtagandet.

#### <a name="view-and-manage-onboarded-sensors"></a>Visa och hantera inbyggda sensorer

På en ny Portal sida för webbplatser och sensorer kan du:

- Lägg till beskrivande information om sensorn. Till exempel en zon som är associerad med sensorn, eller Taggar med fri text.
- Visa och filtrera sensor information. Du kan till exempel Visa information om sensorer som är anslutna till molnet eller lokalt hanterade eller Visa information om sensorer i en speciell zon.  

### <a name="usability"></a>Användbarhet

#### <a name="azure-sentinel-new-connector-page"></a>Sidan Azure Sentinel New Connector

Sidan Azure Defender för IoT data Connector i Azure Sentinel har återskapats. Data kopplingen baseras nu på prenumerationer snarare än IoT-hubbar. gör det möjligt för kunderna att hantera sin konfigurations anslutning till Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>Azure Portal behörighets uppdateringar  

Security Reader och Security Administrator support har lagts till.

### <a name="other-updates"></a>Övriga uppdateringar

#### <a name="access-group---zone-permissions"></a>Åtkomst grupp – zon behörigheter
  
Åtkomst grupps reglerna för den lokala hanterings konsolen innehåller inte alternativet att bevilja åtkomst till en speciell zon. Det finns ingen ändring i Definiera regler som använder platser, regioner och affär senheter.   Efter uppgraderingen kommer åtkomst grupper som innehåller regler som tillåter åtkomst till vissa zoner att ändras för att tillåta åtkomst till dess överordnade plats, inklusive alla dess zoner.

#### <a name="terminology-changes"></a>Ändringar i terminologi

Termen till gång har bytt namn till enheten i sensorn och den lokala hanterings konsolen, rapporter och andra lösnings gränssnitt.
I sensor-och lokal hanterings konsol aviseringar har termen hantera den här händelsen fått reparations steg.

## <a name="next-steps"></a>Nästa steg

[Komma igång med Defender för IoT](getting-started.md)
