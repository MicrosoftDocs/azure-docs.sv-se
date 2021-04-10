---
title: Hantera inaktuella enheter i Azure AD | Microsoft Docs
description: Lär dig hur du tar bort inaktuella enheter från din databas med registrerade enheter i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6774e172c4e8607bfadd95f947d650afa62a984f
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550430"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>Gör så här: hantera inaktuella enheter i Azure AD

Vi rekommenderar att avregistrera registrerade enheter när det inte behövs längre, och när livscykeln ska avslutas. Men på grund av, till exempel, förlorade, stulna eller trasiga enheter, eller ominstallationer av operativsystem har du vanligtvis enheter i din miljö. Som IT-administratör vill du troligen ha en metod för att ta bort inaktuella enheter, så att du kan fokusera dina resurser på att hantera enheter som faktiskt kräver hantering.

I den här artikeln lär du dig hur du på ett effektivt sätt hanterar inaktuella enheter i din miljö.
  

## <a name="what-is-a-stale-device"></a>Vad är en inaktuell enhet?

En inaktuell enhet är en enhet som har registrerats i Azure AD men inte har använts för åtkomst till några molnappar under en viss tid. Inaktuella enheter har en påverkan på din möjlighet att hantera och stödja dina enheter och användare i klientorganisationen eftersom: 

- Dubblettenheter kan göra det svårt för supportpersonalen att identifiera vilken enhet som är aktiv.
- Ett ökat antal enheter skapar onödiga enhets tillbakaskrivningar och ökar tiden för Azure AD Connect-synkroniseringar.
- Som allmän hygien och för att uppfylla efterlevnadskrav kan du vilja ha ett rent tillstånd med enheter. 

Inaktuella enheter i Azure AD kan störa de allmänna livscykelprinciperna för enheter i din organisation.

## <a name="detect-stale-devices"></a>Identifiera inaktuella enheter

Eftersom en inaktuell enhet definieras som en registrerad enhet som inte har används för åtkomst till några molnappar under en viss tid kräver identifieringen av inaktuella enheter en tidsstämpelrelaterad egenskap. I Azure AD kallas egenskapen **ApproximateLastLogonTimestamp** eller **aktivitetstidsstämpel**. Om delta mellan nu och värdet för **aktivitetsstämpeln** överskrider tidsintervallet du har definierat för aktiva enheter anses en enhet vara inaktuell. Denna **aktivitetsstämpel** är nu en offentlig förhandsversion.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Hur hanteras värdet för aktivitetsstämpeln?  

Utvärderingen av aktivitetsstämpeln utlöses av ett autentiseringsförsök för en enhet. Azure AD utvärderar aktivitetsstämpeln när:

- En princip för villkorlig åtkomst som kräver [hanterade enheter](../conditional-access/require-managed-devices.md) eller [godkända klient program](../conditional-access/app-based-conditional-access.md) har utlösts.
- Windows 10-enheter som är Azure AD-anslutna eller Hybrid Azure AD-anslutna är aktiva i nätverket. 
- Intune-hanterade enheter har checkats in i tjänsten.

Om deltaet mellan det befintliga värdet för aktivitetens tidsstämpel och det aktuella värdet är mer än 14 dagar (+/-5 dagars avvikelse), ersätts det befintliga värdet med det nya värdet.

## <a name="how-do-i-get-the-activity-timestamp"></a>Hur gör jag för att hämta aktivitetsstämpeln?

Du har två alternativ för att hämta aktivitetsstämpelns värde:

- Kolumnen **Aktivitet** på [enhetssidan](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) i Azure-portalen

    :::image type="content" source="./media/manage-stale-devices/01.png" alt-text="Skärm bild av en sida i Azure Portal visar namn, ägare och annan information på enheterna. En kolumn visar aktivitetens tidstämpel." border="false":::

- Cmdleten [Get-AzureADDevice](/powershell/module/azuread/Get-AzureADDevice)

    :::image type="content" source="./media/manage-stale-devices/02.png" alt-text="Skärm bild som visar utdata från kommando raden. En rad är markerad och visar en tidstämpel för ApproximateLastLogonTimeStamp-värdet." border="false":::

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Planera rensningen av dina inaktuella enheter

Om du vill rensa inaktuella enheter effektivt i din miljö bör du definiera en relaterad princip. Med den här principen kan du fånga upp alla överväganden som rör inaktuella enheter. I följande avsnitt får du exempel på vanliga principöverväganden. 

### <a name="cleanup-account"></a>Rensningskonto

Om du vill uppdatera en enhet i Azure AD behöver du ett kont som har någon av följande roller tilldelade:

- Global administratör
- Moln enhets administratör
- Intune Service-administratör

I din rensningsprincip väljer du konton som har de roller som krävs tilldelade. 

### <a name="timeframe"></a>Tidsram

Definiera en tidsram som är din indikator för en inaktuell enhet. När du definierar din tidsram anger du för att uppdatera aktivitetens tidstämpel till ditt värde. Anta till exempel att du inte vill ha en tidsstämpel som är yngre än 21 dagar (inklusive varians) som en indikator för en inaktuell enhet. Det finns scenarier som kan göra så att en enhet ser inaktuell ut när den inte är det. Till exempel kan ägaren av den berörda enheten vara på semester eller vara sjuk.  som överskrider tidsramen för inaktuella enheter.

### <a name="disable-devices"></a>Inaktivera enheter

Det är inte lämpligt att ta bort en enhet direkt som verkar vara inaktuell eftersom du inte kan ångra en borttagning om det har gjorts en falsk positiv identifiering. Vi rekommenderar att inaktivera en enhet under en respitperiod innan den tas bort. Definiera i din princip en tidsram för att inaktivera en enhet innan den tas bort.

### <a name="mdm-controlled-devices"></a>MDM-kontrollerade enheter

Om din enhet kontrolleras av Intune eller någon annan MDM-lösning fasar du ut enheten i hanteringssystemet innan du inaktiverar eller ta bort den.

### <a name="system-managed-devices"></a>Systemhanterade enheter

Ta inte bort systemhanterade enheter. Detta är vanligt vis enheter som autopilot. De här enheterna kan inte reserveras när de har tagits bort. Den nya cmdleten `Get-AzureADDevice` exkluderar systemhanterade enheter som standard. 

### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

Dina Hybrid Azure AD-anslutna enheter bör följa dina principer för hantering av lokala inaktuella enheter. 

Rensa Azure AD:

- **Windows 10-enheter** – Inaktivera eller ta bort Windows 10-enheter i din lokala AD och låt Azure AD Connect synkronisera den ändra enhetsstatusen med Azure AD.
- **Windows 7/8** – inaktivera eller ta bort Windows 7/8-enheter i din lokala AD först. Du kan inte använda Azure AD Connect till att inaktivera eller ta bort enheter med Windows 7/8 i Azure AD. I stället måste du inaktivera/ta bort i Azure AD när du gör ändringen lokalt.

> [!NOTE]
>* Att ta bort enheter i din lokala AD eller Azure AD tar inte bort registreringen på klienten. Den förhindrar bara åtkomst till resurser som använder enhet som en identitet (t. ex. villkorlig åtkomst). Läs ytterligare information om hur du [tar bort registreringen på klienten](faq.yml).
>* Om du bara tar bort en Windows 10-enhet i Azure AD synkroniseras enheten om från din lokala dator med hjälp av Azure AD Connect, men som ett nytt objekt i väntande tillstånd. En omregistrering krävs på enheten.
>* Om du tar bort enheten från Sync-omfånget för enheter med Windows 10/Server 2016 tas Azure AD-enheten bort. Om du lägger tillbaka den till Sync-omfånget placeras ett nytt objekt i "väntande" läge. En omregistrering av enheten krävs.
>* Om du inte använder Azure AD Connect för Windows 10-enheter som ska synkroniseras (t. ex. endast genom att använda AD FS för registrering) måste du hantera livs cykeln som liknar Windows 7/8-enheter.


### <a name="azure-ad-joined-devices"></a>Azure AD-anslutna enheter

Inaktivera eller ta bort Azure AD-anslutna enheter i Azure AD.

> [!NOTE]
>* Att ta bort en Azure AD-enhet tar inte bort registreringen på klienten. Den förhindrar bara åtkomst till resurser som använder enhet som en identitet (t. ex. villkorlig åtkomst). 
>* Läs mer om [hur du kopplar från i Azure AD](faq.yml) 

### <a name="azure-ad-registered-devices"></a>Azure AD-registrerade enheter

Inaktivera eller ta bort Azure AD-registrerade enheter i Azure AD.

> [!NOTE]
>* Att ta bort en registrerad Azure AD-enhet i Azure AD tar inte bort registreringen på klienten. Den förhindrar bara åtkomst till resurser som använder enhet som en identitet (t. ex. villkorlig åtkomst).
>* Läs mer om [hur du tar bort en registrering på klienten](faq.yml)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Rensa inaktuella enheter i Azure-portalen  

Du kan rensa inaktuella enheter i Azure-portalen men det är effektivare att hantera processen med hjälp av ett PowerShell-skript. Använd den senaste PowerShell v1-modulen för att använda tids stämplings filtret och för att filtrera ut systemhanterade enheter, till exempel autopilot. Här rekommenderar vi inte att använda PowerShell V2.

En typisk rutin består av följande steg:

1. Ansluta till Azure Active Directory med cmdleten [Connect-AzureAD](/powershell/module/azuread/connect-azuread)
1. Hämta listan över enheter
1. Inaktivera enheten med cmdleten [set-AzureADDevice](/powershell/module/azuread/Set-AzureADDevice) (inaktivera med alternativet-AccountEnabled). 
1. Vänta på respitperioden för det angivna antalet dagar innan du tar bort enheten.
1. Ta bort enheten med cmdleten [Remove-AzureADDevice](/powershell/module/azuread/Remove-AzureADDevice) .

### <a name="get-the-list-of-devices"></a>Hämta listan över enheter

Hämta alla enheter och lagra returnerade data i en CSV-fil:

```PowerShell
Get-AzureADDevice -All:$true | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Om du har ett stort antal enheter i katalogen använder du filtret tidsstämpel för att begränsa antalet returnerade enheter. Hämta alla enheter med en äldre tidsstämpel än ett visst datum och lagra returnerade data i en CSV-fil: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-AzureADDevice -All:$true | Where {$_.ApproximateLastLogonTimeStamp -le $dt} | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Det här bör du veta

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Varför uppdateras inte tidsstämpeln oftare?

Tidsstämpeln uppdateras för att stödja enhetslivscykelscenarier. Det här är ingen granskning. Använd granskningsloggarna för inloggning för mer frekventa uppdateringar på enheten.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Varför bör jag bekymra mig om mina BitLocker-nycklar?

När BitLocker-nycklar för Windows 10-enheter har konfigurerats lagras de på enhetsobjektet i Azure AD. Om du tar bort en inaktuell enhet tar du även bort de BitLocker-nycklar som lagras på enheten. Du bör fastställa om din rensningsprincip överensstämmer med den faktiska livscykeln för din enhet innan du tar bort en inaktuell enhet. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Varför ska jag oroa mig för Windows autopilot-enheter?

När du tar bort en Azure AD-enhet som är kopplad till ett Windows autopilot-objekt kan följande tre scenarier uppstå om enheten kommer att användas i framtiden:
- Med Windows autopilot-baserade distributioner utan att använda för etablering, kommer en ny Azure AD-enhet att skapas, men den märks inte med ZTDID.
- Med distribution i Windows autopilot-distribution med automatisk distribution kan de Miss förfalla eftersom det inte går att hitta en associerad Azure AD-enhet.  (Det här är en säkerhetsmekanism för att se till att ingen "inposts"-enheter försöker ansluta till Azure AD utan autentiseringsuppgifter.) Felet indikerar att en ZTDID matchar inte.
- Med distributioner av Windows autopilot-distributioner kommer de inte att fungera eftersom en tillhör ande Azure AD-enhet inte kan hittas. (I bakgrunden använder för etablerings distributioner samma självdistribuerade läge, så att de tvingar samma säkerhetsmekanismer.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Hur tar jag reda på alla typer av anslutna enheter?

Mer information om de olika typerna finns i [enhetshanteringsöversikten](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Vad händer när jag inaktiverar en enhet?

Alla autentiseringar där en enhet används för att autentisera för Azure AD nekas. Vanliga exempel:

- **Hybrid Azure AD-ansluten enhet** – användarna kan använda enheten för att logga in på den lokala domänen. De kan dock inte komma åt Azure AD-resurser som Microsoft 365.
- **Azure AD-ansluten enhet** – Användare kan inte använda enheten till att logga in. 
- **Mobila enheter** – användaren kan inte komma åt Azure AD-resurser som Microsoft 365. 

## <a name="next-steps"></a>Nästa steg

Om du vill ha en översikt över hantering av enheter i Azure-portalen kan du läsa om att [hantera enheter med Azure-portalen](device-management-azure-portal.md)
