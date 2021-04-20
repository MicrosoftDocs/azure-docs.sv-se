---
title: Vad är nytt i Azure Defender for IoT
description: I den här artikeln får du veta vad som är nytt i den senaste versionen av Defender for IoT.
ms.topic: overview
ms.date: 4/6/2021
ms.openlocfilehash: df6a43dc68acd025b1c65877c65d1b7e947f210b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739394"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Vad är nytt i Azure Defender for IoT?

Den här artikeln innehåller nya funktioner och funktionsförbättringar för Defender for IoT.

Antecknade funktioner finns i FÖRHANDSVERSION. Tilläggsvillkor [för Azure-förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som ännu inte har släppts i allmän tillgänglighet.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Versionshantering och stöd för Azure Defender for IoT 

Nedan visas stöd, policyer för att bryta ändringar för Defender for IoT och de versioner av Azure Defender for IoT som är tillgängliga för närvarande. 

### <a name="servicing-information-and-timelines"></a>Serviceinformation och tidslinjer 

Microsoft planerar att släppa uppdateringar för Azure Defender for IoT mindre än en gång per kvartal. Varje allmän tillgänglighetsversion (GA) av Azure Defender for IoT sensorn och den lokala hanteringskonsolen stöds i upp till nio månader efter att den har släppts. Korrigeringar och nya funktioner kommer att tillämpas på den aktuella GA-versionen som för närvarande har stöd och kommer inte att tillämpas på äldre GA-versioner.

### <a name="versions-and-support-dates"></a>Versioner och supportdatum

| Version | Frisläppt datum | Slutdatum för support |
|--|--|--|
| 10,0 | 01/2021 | 10/2021 |
## <a name="march-2021"></a>Mars 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Sensor – förbättrade anpassade aviseringsregler (offentlig förhandsversion)

Nu kan du skapa anpassade aviseringsregler baserat på dag, grupp med dagar och tidsperiod då nätverksaktiviteten upptäcktes.  Att arbeta med villkor för dag- och tidsregel är användbart, till exempel i fall där allvarlighetsgraden för aviseringen härleds när aviseringshändelsen äger rum. Du kan till exempel skapa en anpassad regel som utlöser en varning med hög allvarlighetsgrad när nätverksaktivitet identifieras en helg eller på kvällen.

Den här funktionen är tillgänglig på sensorn med version 10.2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Lokal hanteringskonsol – exportera aviseringar (offentlig förhandsversion)

Aviseringsinformation kan nu exporteras till en CSV-fil från den lokala hanteringskonsolen. Du kan exportera information om alla aviseringar som identifierats eller exportera information baserat på den filtrerade vyn.

Den här funktionen är tillgänglig i den lokala hanteringskonsolen med version 10.2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Lägg till ett andra nätverksgränssnitt i den lokala hanteringskonsolen (offentlig förhandsversion)

Nu kan du förbättra säkerheten för distributionen genom att lägga till ett andra nätverksgränssnitt i din lokala hanteringskonsol. Med den här funktionen kan din lokala hantering ha anslutna sensorer i ett säkert nätverk, samtidigt som användarna får åtkomst till den lokala hanteringskonsolen via ett andra separat nätverksgränssnitt.

Den här funktionen är tillgänglig i den lokala hanteringskonsolen med version 10.2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Enhetsbyggare – ny mikroagent (offentlig förhandsversion)

En ny modul för enhetsbyggare är tillgänglig. Modulen, som kallas en mikroagent, tillåter:

- **Integrering med Azure IoT Hub och Azure Defender for IoT** – skapa starkare slutpunktssäkerhet direkt i dina IoT-enheter genom att integrera den med övervakningsalternativet som tillhandahålls av både Azure IoT Hub och Azure Defender for IoT.
- Flexibla distributionsalternativ med stöd för **standard-IoT-operativsystem** – kan distribueras antingen som ett binärt paket eller som ändringsbar källkod, med stöd för IoT-standardoperativsystemet som Linux och Azure RTOS.
- **Minimala resurskrav utan OS-kernelberoenden** – litet fotavtryck, låg CPU-förbrukning och inga OS-kernelberoenden.
- **Hantering av säkerhetsstatus** – övervaka säkerhetsstatusen för dina IoT-enheter proaktivt.
- **Kontinuerlig IoT-/OT-hotidentifiering** i realtid – identifiera hot som botnät, råstyrkkraftsförsök, kryptogrävare och misstänkt nätverksaktivitet

Den inaktuella dokumentationen för Defender-IoT-micro-agent kommer att flyttas till den agentbaserade lösningen för *enhetsbyggare>den klassiska* mappen.

Den här funktionsuppsättningen är tillgänglig med den aktuella offentliga förhandsversionen av molnet.

## <a name="january-2021"></a>Januari 2021

- [Säkerhet](#security)
- [Registrering](#onboarding)
- [Användbarhet](#usability)
- [Övriga uppdateringar](#other-updates)
### <a name="security"></a>Säkerhet

Förbättringar av certifikat- och lösenordsåterställning har gjorts för den här versionen.

#### <a name="certificates"></a>Certifikat
  
Med den här versionen kan du:

- Ladda upp SSL-certifikat direkt till sensorer och lokala hanteringskonsoler.
- Utför validering mellan den lokala hanteringskonsolen och anslutna sensorer, samt mellan en hanteringskonsol och en hanteringskonsol med hög tillgänglighet. Valideringen baseras på förfallodatum, rotcertifikatutfärdares äkthet och listor över återkallade certifikat.  Om verifieringen misslyckas fortsätter inte sessionen.

För uppgraderingar:

- Ssl-certifikat eller valideringsfunktioner ändras inte under uppgraderingen.
- Efter uppgraderingen kan administrativa användare av sensor- och lokal hanteringskonsol ersätta SSL-certifikat eller aktivera SSL-certifikatverifiering från fönstret Systeminställningar, SSL-certifikat.  

För nya installationer:

- Vid första inloggningen måste användarna antingen använda ett SSL-certifikat (rekommenderas) eller ett lokalt genererat självloggat certifikat (rekommenderas inte)
- Certifikatverifiering är aktiverat som standard för nya installationer.

#### <a name="password-recovery"></a>Återställning av lösenord
  
Sensor- och lokal hanteringskonsol Administrativa användare kan nu återställa lösenord från Azure Defender for IoT portalen. Tidigare krävde supportteamet åtgärder för återställning av lösenord.

### <a name="onboarding"></a>Publicering

#### <a name="on-premises-management-console---committed-devices"></a>Lokal hanteringskonsol – enheter som har utförts

Efter den första inloggningen till den lokala hanteringskonsolen måste användarna nu ladda upp en aktiveringsfil. Filen innehåller det sammanlagda antalet enheter som ska övervakas i organisationsnätverket. Det här numret kallas för antalet indeerade enheter.
Intända enheter definieras under registreringsprocessen på Azure Defender for IoT portalen, där aktiveringsfilen genereras.
Första gången användare och användare uppgraderar krävs för att ladda upp aktiveringsfilen.
Efter den första aktiveringen kan antalet enheter som har identifierats i nätverket överskrida antalet enheter som har utförts. Den här händelsen kan till exempel inträffa om du ansluter fler sensorer till hanteringskonsolen. Om det finns en skillnad mellan antalet identifierade enheter och antalet enheter som har utförts visas en varning i hanteringskonsolen. Om den här händelsen inträffar bör du ladda upp en ny aktiveringsfil.

#### <a name="pricing-page-options"></a>Alternativ för prissättningssidan

På prissättningssidan kan du registrera nya prenumerationer för att Azure Defender for IoT och definiera inbyggda enheter i nätverket.  
Dessutom kan du på sidan Prissättning nu hantera befintliga prenumerationer som är kopplade till en sensor och uppdatera enhetsåtagandet.

#### <a name="view-and-manage-onboarded-sensors"></a>Visa och hantera inbyggda sensorer

På en ny portalsida för plats och sensorer kan du:

- Lägg till beskrivande information om sensorn. Till exempel en zon som är associerad med sensorn eller fritexttaggar.
- Visa och filtrera sensorinformation. Du kan till exempel visa information om sensorer som är molnanslutna eller lokalt hanterade, eller visa information om sensorer i en specifik zon.  

### <a name="usability"></a>Användbarhet

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel sidan för ny anslutningsapp

Sidan Azure Defender for IoT dataanslutning i Azure Sentinel har gjorts om. Dataanslutningen baseras nu på prenumerationer i stället för IoT Hubs. gör det möjligt för kunder att bättre hantera sin konfigurationsanslutning till Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>Azure Portal behörighetsuppdateringar  

Stöd för säkerhetsläsare och säkerhetsadministratör har lagts till.

### <a name="other-updates"></a>Övriga uppdateringar

#### <a name="access-group---zone-permissions"></a>Åtkomstgrupp – zonbehörigheter
  
Åtkomstgruppsreglerna för den lokala hanteringskonsolen innehåller inte alternativet att bevilja åtkomst till en specifik zon. Det finns ingen ändring i att definiera regler som använder webbplatser, regioner och affärsenheter.   Efter uppgraderingen ändras åtkomstgrupper som innehåller regler som tillåter åtkomst till specifika zoner för att tillåta åtkomst till den överordnade platsen, inklusive alla dess zoner.

#### <a name="terminology-changes"></a>Ändringar i terminologi

Termen tillgång har bytt namn till enhet i sensor- och lokal hanteringskonsolen, rapporter och andra lösningsgränssnitt.
I aviseringar för sensor- och lokal hanteringskonsol har termen Hantera den här händelsen fått namnet Reparationssteg.

## <a name="next-steps"></a>Nästa steg

[Komma igång med Defender for IoT](getting-started.md)
