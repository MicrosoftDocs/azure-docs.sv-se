---
title: Övervaka lokalt Azure AD-lösenord för lösen ords skydd
description: Lär dig hur du övervakar och granskar loggar för Azure AD Password Protection för en lokal Active Directory Domain Services miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: edc246a414401c4c1c0248787eda0381fcd63037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741770"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Övervaka och granska loggar för lokala Azure AD-miljöer för lösen ords skydd

Efter distributionen av lösen ords skydd i Azure AD är övervakning och rapportering viktiga uppgifter. Den här artikeln innehåller information som hjälper dig att förstå olika övervaknings tekniker, inklusive var varje tjänst loggar information och hur du rapporterar om användningen av lösen ords skydd i Azure AD.

Övervakning och rapportering görs antingen av händelse logg meddelanden eller genom att köra PowerShell-cmdlets. DC-agenten och proxy Services loggar både logg meddelanden för händelse loggen. Alla PowerShell-cmdletar som beskrivs nedan är bara tillgängliga på proxyservern (se AzureADPasswordProtection PowerShell-modulen). Program varan för DC-agenten installerar inte någon PowerShell-modul.

## <a name="dc-agent-event-logging"></a>Händelse loggning för DC-agent

På varje domänkontrollant skriver DC agent service-programvaran resultaten av varje enskild lösen ords validerings åtgärd (och annan status) till en lokal händelse logg:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Administratörs loggen för DC-agenten är den främsta informations källan för hur program varan fungerar.

Observera att spårnings loggen är inaktive rad som standard.

Händelser som loggats av olika komponenter för DC-agenten ligger inom följande intervall:

|Komponent |Intervall för händelse-ID|
| --- | --- |
|DLL för lösen ords filter för DC-agent| 10000-19999|
|Värd process för DC-agenttjänsten| 20000-29999|
|Validerings logik för DC-agentens tjänst princip| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Händelse logg för DC-agentens administratör

### <a name="password-validation-outcome-events"></a>Händelse av lösen ords verifierings resultat

På varje domänkontrollant skriver DC agent service-programvaran resultaten av varje enskild lösen ords verifiering till händelse loggen för DC-agenten.

För att det ska gå att verifiera lösen ord finns det vanligt vis en händelse som loggas från dll-filen med lösen ords filter för DC För att det ska gå att verifiera lösen ord, finns det vanligt vis två händelser som loggats, en från DC-agenttjänsten och en från dll-filen för lösen ords filter för DC-agent.

Diskreta händelser för att avbilda dessa situationer loggas utifrån följande faktorer:

* Anger om ett angivet lösen ord ska anges eller ändras.
* Om validering av ett angivet lösen ord har skickats eller misslyckats.
* Om verifieringen misslyckades på grund av den globala Microsoft-principen, organisations principen eller en kombination.
* Om läget endast granskning för närvarande är aktiverat eller inte för den aktuella lösen ords principen.

De viktigaste händelserna för lösen ords validering är följande:

| Händelse |Lösenordsändring |Lösen ords uppsättning|
| --- | :---: | :---: |
|Godkänd |10014 |10015|
|Misslyckande (på grund av lösen ords princip för kunder)| 10016, 30002| 10017, 30003|
|Misslyckande (på grund av lösen ords princip från Microsoft)| 10016, 30004| 10017, 30005|
|Misslyckad (på grund av kombinerad lösen ords princip för Microsoft och kunder)| 10016, 30026| 10017, 30027|
|Misslyckande (på grund av användar namn)| 10016, 30021| 10017, 30022|
|Endast gransknings pass (skulle ha misslyckats med kundens lösen ords princip)| 10024, 30008| 10025, 30007|
|Endast gransknings pass (den skulle ha misslyckats med Microsofts lösen ords princip)| 10024, 30010| 10025, 30009|
|Endast gransknings pass (skulle ha misslyckats med att kombinera principer för lösen ord för Microsoft och kunden)| 10024, 30028| 10025, 30029|
|Endast gransknings pass (misslyckades på grund av användar namn)| 10016, 30024| 10017, 30023|

De fall i tabellen ovan som refererar till "kombinerade principer" avser situationer där en användares lösen ord hittades som innehåller minst en token från både Microsoft-listan över förbjudna lösen ord och listan över blockerade lösen ord för kunder.

De fall i tabellen ovan som hänvisar till "användar namn" hänvisar till situationer där en användares lösen ord hittades som antingen innehåller användarens konto namn och/eller en av användarens egna namn. Scenariot innebär att användarens lösen ord avvisas när principen är inställd på Genomdriv eller skickas om principen är i gransknings läge.

När ett par med händelser loggas tillsammans, associeras båda händelserna explicit genom att ha samma CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Sammanfattnings rapportering av lösen ords validering via PowerShell

`Get-AzureADPasswordProtectionSummaryReport`Cmdleten kan användas för att skapa en sammanfattande vy över aktiviteten för lösen ords validering. Exempel på utdata från denna cmdlet är följande:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Omfattningen av cmdletens rapportering kan påverkas av en av parametrarna – skog,-Domain eller – DomainController. Att inte ange en parameter innebär – skog.

`Get-AzureADPasswordProtectionSummaryReport`Cmdleten fungerar genom att köra en fråga till händelse loggen för DC-agenten och sedan räkna in det totala antalet händelser som motsvarar varje visad resultat kategori. Följande tabell innehåller mappningarna mellan varje utfall och dess motsvarande händelse-ID:

|Get-AzureADPasswordProtectionSummaryReport egenskap |Motsvarande händelse-ID|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Observera att `Get-AzureADPasswordProtectionSummaryReport` cmdleten levereras i PowerShell-skript och om det behövs kan hänvisas direkt till följande plats:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Den här cmdleten fungerar genom att öppna en PowerShell-session till varje domänkontrollant. För att lyckas måste stöd för PowerShell-fjärrsession vara aktiverat på varje domänkontrollant och klienten måste ha tillräcklig behörighet. Mer information om kraven för PowerShell-fjärrsessioner får du genom att köra "Get-Help about_Remote_Troubleshooting" i ett PowerShell-fönster.

> [!NOTE]
> Den här cmdleten fungerar genom att skicka frågor till varje administratörs händelse logg för DC-agenten. Om händelse loggarna innehåller ett stort antal händelser kan cmdleten ta lång tid att slutföra. Dessutom kan Mass nätverks frågor för stora data uppsättningar påverka domänkontrollantens prestanda. Därför bör denna cmdlet användas noggrant i produktions miljöer.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Exempel på händelse logg meddelande för händelse-ID 10014 (lösen ords ändring lyckades)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Exempel på händelse logg meddelande för händelse-ID 10017 och 30003 (uppsättning med misslyckade lösen ord)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Exempel på händelse logg meddelande för händelse-ID 30001 (lösen ord accepteras eftersom ingen princip är tillgänglig)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Exempel på händelse logg meddelande för händelse-ID 30006 (ny princip tillämpas)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Exempel på händelse logg meddelande för händelse-ID 30019 (Azure AD Password Protection är inaktive rad)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Drift logg för DC-agent

DC-agenttjänsten kommer också att logga operativa händelser till följande logg:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Spårnings logg för DC-agent

DC-agenttjänsten kan också logga utförliga spårnings händelser på fel söknings nivå till följande logg:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Spårnings loggning är inaktiverat som standard.

> [!WARNING]
> När det här alternativet är aktiverat får spårnings loggen en stor mängd händelser och kan påverka domänkontrollantens prestanda. Därför bör den här utökade loggen bara aktive ras när ett problem kräver djupare undersökningar och sedan bara under en minimal tid.

## <a name="dc-agent-text-logging"></a>Text loggning för DC-agent

DC-agenttjänsten kan konfigureras att skriva till en text logg genom att ange följande register värde:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Text loggning är inaktiverat som standard. En omstart av DC-agenttjänsten krävs för att ändringar av det här värdet ska börja gälla. När den här inställningen är aktive rad skrivs DC-agenttjänsten till en loggfil som finns under:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Text loggen får samma fel söknings nivå poster som kan loggas i spårnings loggen, men är ofta i ett enklare format för att granska och analysera.

> [!WARNING]
> När den här loggen är aktive rad tar den en stor mängd händelser och kan påverka domänkontrollantens prestanda. Därför bör den här utökade loggen bara aktive ras när ett problem kräver djupare undersökningar och sedan bara under en minimal tid.

## <a name="dc-agent-performance-monitoring"></a>Prestanda övervakning av DC-agent

Program varan för DC-agenten installerar ett prestanda räknar objekt med namnet **Azure AD Password Protection**. Följande prestanda räknare är tillgängliga för närvarande:

|Prestanda räknar namn | Beskrivning|
| --- | --- |
|Bearbetade lösen ord |Den här räknaren visar det totala antalet bearbetade lösen ord (accepterade eller avvisade) sedan den senaste omstarten.|
|Accepterade lösen ord |Den här räknaren visar det totala antalet lösen ord som har godkänts sedan den senaste omstarten.|
|Nekade lösen ord |Den här räknaren visar det totala antalet lösen ord som har avvisats sedan den senaste omstarten.|
|Begär Anden om lösen ords filter pågår |Den här räknaren visar antalet begär Anden om lösen ords filter som pågår.|
|Högsta antal begär Anden om lösen ords filter |Den här räknaren visar det högsta antalet samtidiga begär Anden om lösen ords filter sedan den senaste omstarten.|
|Fel vid begäran om lösen ords filter |Den här räknaren visar det totala antalet begär Anden om lösen ords filter som misslyckades på grund av ett fel sedan den senaste omstarten. Fel kan uppstå när tjänsten Azure AD Password Protection DC agent inte körs.|
|Begär Anden om lösen ords filter/SEK |Den här räknaren visar den hastighet med vilken lösen ord bearbetas.|
|Bearbetnings tid för begäran om lösen ords filter |Den här räknaren visar den genomsnittliga tid som krävs för att bearbeta en begäran om lösen ords filter.|
|Bearbetnings tid för begäran om högsta lösen ords filter |Den här räknaren visar bearbetnings tiden för antalet tillåtna lösen ords filter sedan den senaste omstarten.|
|Lösen ord som accepterats på grund av gransknings läge |Den här räknaren visar det totala antalet lösen ord som normalt skulle ha avvisats, men som har godkänts eftersom lösen ords principen har kon figurer ATS för gransknings läge (sedan senaste omstart).|

## <a name="dc-agent-discovery"></a>Identifiering av DC-agent

`Get-AzureADPasswordProtectionDCAgent`Cmdleten kan användas för att visa grundläggande information om de olika DC-agenter som körs i en domän eller skog. Den här informationen hämtas från de serviceConnectionPoint-objekt som registreras av som kör DC-agenttjänsten.

Exempel på utdata från denna cmdlet är följande:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

De olika egenskaperna uppdateras av varje DC-agenttjänsten i en ungefärlig timme. Data omfattas fortfarande Active Directory replikeringsfördröjning.

Omfattningen av cmdletens fråga kan påverkas av parametrarna-skog eller – domän.

Om värdet HeartbeatUTC är inaktuellt kan detta vara ett symtom på att Azure AD Password Protection DC-agenten inte körs eller har avinstallerats, eller att datorn har degraderats och inte längre är en domänkontrollant.

Om värdet PasswordPolicyDateUTC är inaktuellt kan detta vara ett symtom på att Azure AD Password Protection DC-agenten på den datorn inte fungerar korrekt.

## <a name="dc-agent-newer-version-available"></a>En nyare version av DC-agent är tillgänglig

DC-agenttjänsten loggar en 30034 varnings händelse till den operativa loggen vid identifiering av att en nyare version av DC-agentens program vara är tillgänglig, till exempel:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

Händelsen ovan anger inte versionen för den nya program varan. Du bör gå till länken i händelse meddelandet för denna information.

> [!NOTE]
> Trots referenserna till "autoupgrade" i ovanstående händelse meddelande, stöder inte DC-agentprogram varan den här funktionen.

## <a name="proxy-service-event-logging"></a>Händelse loggning för proxy service

Proxyservern avger en minimal uppsättning händelser för följande händelse loggar:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Observera att spårnings loggen är inaktive rad som standard.

> [!WARNING]
> När den är aktive rad får spårnings loggen en stor mängd händelser och detta kan påverka prestandan för proxyservern. Därför bör den här loggen bara aktive ras när ett problem kräver djupare undersökningar och sedan bara under en minimal tid.

Händelser loggas av de olika proxy-komponenterna med följande intervall:

|Komponent |Intervall för händelse-ID|
| --- | --- |
|Värd process för proxy-tjänst| 10000-19999|
|Proxy service Core affärs logik| 20000-29999|
|PowerShell-cmdletar| 30000-39999|

## <a name="proxy-service-text-logging"></a>Text loggning för proxy service

Proxy-tjänsten kan konfigureras att skriva till en text logg genom att ange följande register värde:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD värde)

Text loggning är inaktiverat som standard. En omstart av proxy-tjänsten krävs för att ändringar av det här värdet ska börja gälla. När den är aktive rad skrivs proxyservern till en loggfil som finns under:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Text loggen får samma fel söknings nivå poster som kan loggas i spårnings loggen, men är ofta i ett enklare format för att granska och analysera.

> [!WARNING]
> När den här loggen är aktive rad tar den en stor mängd händelser och kan påverka datorns prestanda. Därför bör den här utökade loggen bara aktive ras när ett problem kräver djupare undersökningar och sedan bara under en minimal tid.

## <a name="powershell-cmdlet-logging"></a>PowerShell-cmdlet-loggning

PowerShell-cmdletar som resulterar i en tillstånds ändring (till exempel register-AzureADPasswordProtectionProxy) loggar normalt en resultat händelse i drift loggen.

Dessutom skrivs de flesta av PowerShell-cmdletarna för Azure AD Password Protection till en text logg som finns under:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Om ett cmdlet-fel uppstår och orsaken och/eller-lösningen inte är tydligare kan dessa text loggar också konsulteras.

## <a name="proxy-discovery"></a>Identifiering av proxy

`Get-AzureADPasswordProtectionProxy`Cmdleten kan användas för att visa grundläggande information om de olika proxy-tjänster för Azure AD-lösenordsautentisering som körs i en domän eller skog. Den här informationen hämtas från de serviceConnectionPoint-objekt som registrerats av den eller de Proxy-tjänster som körs.

Exempel på utdata från denna cmdlet är följande:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

De olika egenskaperna uppdateras av varje proxyserver med en ungefärlig per timme. Data omfattas fortfarande Active Directory replikeringsfördröjning.

Omfattningen av cmdletens fråga kan påverkas av parametrarna-skog eller – domän.

Om värdet HeartbeatUTC är inaktuellt kan detta vara ett symtom på att Azure AD-lösenordet för lösen ords skydd på den datorn inte körs eller har avinstallerats.

## <a name="proxy-agent-newer-version-available"></a>En nyare version av proxyagenten är tillgänglig

Proxy-tjänsten kommer att logga en 20002 varnings händelse till den operativa loggen vid identifiering av att en nyare version av proxy-programvaran är tillgänglig, till exempel:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

Händelsen ovan anger inte versionen för den nya program varan. Du bör gå till länken i händelse meddelandet för denna information.

Den här händelsen genereras även om proxyagenten har kon figurer ATS med autoupgrade aktiverat.

## <a name="next-steps"></a>Nästa steg

[Fel sökning av lösen ords skydd i Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Mer information om globala och anpassade listor över blockerade lösen ord finns i artikeln [förbjuda Felaktiga lösen ord](concept-password-ban-bad.md)
