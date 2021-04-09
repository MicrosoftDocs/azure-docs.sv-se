---
title: Felsöka lokalt Azure AD-lösenord för lösen ords skydd
description: Lär dig hur du felsöker Azure AD Password Protection för en lokal Active Directory Domain Services miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2bbc1c555824d4c632c5bf85a9cd0aa83087fc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648733"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Felsöka: lokalt Azure AD-lösenord

Efter distributionen av lösen ords skydd i Azure AD kan det krävas fel sökning. Den här artikeln beskriver hur du kan förstå några vanliga fel söknings steg.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC-agenten kan inte hitta en proxy i katalogen

Huvud symptomet för det här problemet är 30017 händelser i händelse loggen för DC-agenten.

Den vanligaste orsaken till det här problemet är att en proxy ännu inte har registrerats. Om en proxy har registrerats kan det uppstå en fördröjning på grund av svars tiden för AD-replikering tills en viss DC-agent kan se den proxyservern.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC-agenten kan inte kommunicera med en proxy

Huvud symptomet för det här problemet är 30018 händelser i händelse loggen för DC-agenten. Det här problemet kan ha flera möjliga orsaker:

1. DC-agenten finns i en isolerad del av nätverket som inte tillåter nätverks anslutning till den eller de registrerade proxyservrarna. Det här problemet kan vara ofarligt så länge andra DC-agenter kan kommunicera med proxyn eller proxyservrarna för att ladda ned lösen ords principer från Azure. När de har hämtats kommer de principerna att erhållas av den isolerade DOMÄNKONTROLLANTen via replikering av principfiler i Sysvol-resursen.

1. Proxyservern blockerar åtkomst till slut punkten för RPC-slutpunktsmapparen (port 135)

   Installations programmet för Azure AD Password Protection skapar automatiskt en regel för inkommande Windows-brandvägg som ger åtkomst till port 135. Om den här regeln senare tas bort eller inaktive ras kan inte DC-agenter kommunicera med proxyservern. Om den inbyggda Windows-brandväggen har inaktiverats i stället för en annan brand Väggs produkt måste du konfigurera brand väggen så att den tillåter åtkomst till port 135.

1. Proxyservern blockerar åtkomst till RPC-slutpunkten (dynamisk eller statisk) som lyssnar på av proxyservern

   Installations programmet för Azure AD Password Protection skapar automatiskt en regel för inkommande Windows-brandvägg som ger åtkomst till alla inkommande portar som har avlyssnats av proxyn för Azure AD Password Protection. Om den här regeln senare tas bort eller inaktive ras kan inte DC-agenter kommunicera med proxyservern. Om den inbyggda Windows-brandväggen har inaktiverats i stället för en annan brand Väggs produkt måste du konfigurera brand väggen så att den tillåter åtkomst till alla inkommande portar som är lyssnade på Azure AD Password Protection proxy-tjänsten. Den här konfigurationen kan göras mer detaljerad om proxy-tjänsten har kon figurer ATS för att lyssna på en angiven statisk RPC-port (med hjälp av `Set-AzureADPasswordProtectionProxyConfiguration` cmdleten).

1. Proxy-datorn är inte konfigurerad för att tillåta att domänkontrollanter loggar in på datorn. Detta beteende styrs via tilldelningen "åtkomst till den här datorn från nätverket". Alla domänkontrollanter i alla domäner i skogen måste tilldelas den här behörigheten. Den här inställningen är ofta begränsad som en del av belastningen på en större nätverks härdning.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy-tjänsten kan inte kommunicera med Azure

1. Se till att proxyservern har anslutning till de slut punkter som anges i [distributions kraven](howto-password-ban-bad-on-premises-deploy.md).

1. Se till att skogen och alla proxyservrar är registrerade gentemot samma Azure-klient.

   Du kan kontrol lera detta krav genom att  `Get-AzureADPasswordProtectionProxy` köra `Get-AzureADPasswordProtectionDCAgent` -och PowerShell-cmdletarna och sedan jämföra `AzureTenant` egenskapen för varje returnerad artikel. För korrekt åtgärd måste det rapporterade klient namnet vara detsamma för alla DC-agenter och proxyservrar.

   Om ett matchnings fel för ett Azure-klientnummer finns kan det här problemet åtgärdas genom att köra `Register-AzureADPasswordProtectionProxy` och/eller `Register-AzureADPasswordProtectionForest` PowerShell-cmdletar efter behov, och se till att använda autentiseringsuppgifter från samma Azure-klient för alla registreringar.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC-agenten kan inte kryptera eller dekryptera lösen ords princip filen

Azure AD Password Protection är ett kritiskt beroende av de krypterings-och dekrypterings funktioner som tillhandahålls av Microsoft Key Distribution Service. Krypterings-eller dekrypterings fel kan manifesta med en rad olika symtom och ha flera möjliga orsaker.

1. Kontrol lera att KDS-tjänsten är aktive rad och fungerar på alla domänkontrollanter med Windows Server 2012 och senare i en domän.

   Som standard är tjänstens start läge för KDS-tjänsten konfigurerad som manuell (utlösare start). Den här konfigurationen innebär att första gången en klient försöker använda tjänsten startas den på begäran. Standard läget för tjänst start är acceptabelt för att Azure AD-lösenord ska fungera.

   Om KDS-tjänstens start läge har kon figurer ATS att inaktive ras måste den här konfigurationen åtgärdas innan lösen ords skyddet i Azure AD fungerar korrekt.

   Ett enkelt test för det här problemet är att manuellt starta KDS-tjänsten, antingen via MMC-konsolen för service hantering eller med andra hanterings verktyg (t. ex. köra "net start kdssvc" från en kommando tolks konsol). KDS-tjänsten förväntas starta korrekt och fortsätter att köras.

   Den vanligaste rotor saken som KDS-tjänsten inte kan starta är att objektet Active Directory domänkontrollant finns utanför standarddomänkontrollantens ORGANISATIONSENHET. Den här konfigurationen stöds inte av KDS-tjänsten och är inte en begränsning som angetts av Azure AD Password Protection. Korrigeringen för det här tillståndet är att flytta objektet domänkontrollant till en plats under standarddomänkontrollantens ORGANISATIONSENHET.

1. Inkompatibel KDS-krypterad buffertstorlek ändring från Windows Server 2012 R2 till Windows Server 2016

   En säkerhets korrigering för KDS introducerades i Windows Server 2016 som ändrar formatet på KDS-krypterade buffertar. Dessa buffertar kan ibland inte dekrypteras på Windows Server 2012 och Windows Server 2012 R2. Den motsatta riktningen är OK-buffertar som är KDS-krypterade på Windows Server 2012 och Windows Server 2012 R2 kommer alltid att kunna dekryptera på Windows Server 2016 och senare. Om domän kontrol Lanterna i Active Directory domäner kör en blandning av dessa operativ system kan tillfälliga Azure AD-dekryptering av lösen ords skydd rapporteras. Det går inte att förutsäga tiden eller symtomen på dessa problem på ett korrekt sätt med hjälp av säkerhets korrigeringens typ, och eftersom den är icke-deterministisk som DC-agenten för lösen ords skydd för Azure AD som domänkontrollanten kommer att kryptera data vid en specifik tidpunkt.

   Det finns ingen lösning på det här problemet än att inte köra en blandning av dessa inkompatibla operativ system i Active Directory-domänerna. Med andra ord bör du bara köra domänkontrollanter som kör Windows Server 2012 och Windows Server 2012 R2, eller så bör du bara köra Windows Server 2016 och senare domänkontrollanter.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>DC-agenten tror att skogen inte har registrerats

Symptomet på det här problemet är 30016 händelser som registreras i Agent\Admin-kanalen för DC som står i del:

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

Det finns två möjliga orsaker till det här problemet.

1. Skogen har inte registrerats. Lös problemet genom att köra kommandot Register-AzureADPasswordProtectionForest enligt beskrivningen i [distributions kraven](howto-password-ban-bad-on-premises-deploy.md).
1. Skogen har registrerats, men DC-agenten kan inte dekryptera skogens registrerings data. Det här fallet har samma rotor saken som problem #2 som anges ovan under [DC-agenten kan inte kryptera eller dekryptera filer för lösen ords princip](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files). Ett enkelt sätt att bekräfta den här teorien är att du bara ser det här felet på DC-agenter som körs på Windows Server 2012 eller Windows Server 2012R2 domänkontrollanter, medan DC-agenter som körs på Windows Server 2016 och senare domänkontrollanter fungerar. Lösningen är densamma: uppgradera alla domänkontrollanter till Windows Server 2016 eller senare.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Svaga lösen ord godkänns men bör inte

Det här problemet kan ha flera orsaker.

1. DC-agenterna kör en offentlig för hands version av program vara som har upphört att gälla. Se [Public Preview DC Agent-programvaran har upphört att gälla](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Det går inte att hämta en princip eller också går det inte att dekryptera befintliga principer. Sök efter möjliga orsaker i ovanstående avsnitt.

1. Lösen ords principen tvinga fram läge är fortfarande inställt på granskning. Om den här konfigurationen är aktive rad konfigurerar du om den så att den tillämpas med hjälp av Azure AD-portalen för lösen ords skydd. Mer information finns i [drifts lägen](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Lösen ords principen har inaktiverats. Om den här konfigurationen gäller konfigurerar du om den så att den aktive ras med Azure AD-portalen för lösen ords skydd. Mer information finns i [drifts lägen](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Du har inte installerat DC-agentens program vara på alla domänkontrollanter i domänen. I den här situationen är det svårt att se till att fjärranslutna Windows-klienter är riktade mot en viss domänkontrollant under en ändring av lösen ord. Om du tror att du har rätt mål för en viss DOMÄNKONTROLLANT där DC-agenten är installerad, kan du kontrol lera genom att dubbelklicka på händelse loggen för DC-agentens administratör: oavsett resultatet finns det minst en händelse för att dokumentera resultatet av lösen ords verifieringen. Om det inte finns någon händelse för den användare vars lösen ord har ändrats, bearbetades förmodligen lösen ords ändringen av en annan domänkontrollant.

   Som ett alternativt test kan du testa setting\changing-lösenord när du är inloggad direkt på en DOMÄNKONTROLLANT där DC-agentens program vara är installerad. Den här tekniken rekommenderas inte för produktions Active Directorys domäner.

   Även om den stegvisa distributionen av program varan för DC-agenten stöds av dessa begränsningar rekommenderar Microsoft starkt att program varan för DC-agenten är installerad på alla domänkontrollanter i en domän så snart som möjligt.

1. Algoritmen för lösen ords verifiering kan faktiskt fungera som förväntat. Se [hur är lösen ord utvärderas](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe inte ange ett svagt DSRM-lösenord

Active Directory kommer alltid att verifiera ett nytt lösen ord för katalog tjänstens reparations läge för att säkerställa att det uppfyller domänens krav på lösen ords komplexitet. den här verifieringen anropar även DLL-filer för lösen ords filter som Azure AD Password Protection. Om det nya DSRM-lösenordet nekas visas följande fel meddelande:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

När Azure AD Password Protection loggar inloggnings händelser för lösen ord för ett Active Directory DSRM-lösenord förväntas att händelse logg meddelandena inte ska innehålla något användar namn. Det här problemet beror på att DSRM-kontot är ett lokalt konto som inte ingår i den faktiska Active Directorys domänen.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Befordran av replik på domänkontrollanten Miss lyckas på grund av ett svagt DSRM-lösenord

Under befordran av DOMÄNKONTROLLANT skickas lösen ordet för den nya katalog tjänstens reparations läge till en befintlig DOMÄNKONTROLLANT i domänen för verifiering. Om det nya DSRM-lösenordet nekas visas följande fel meddelande:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Precis som i ovanstående problem har alla resultat för lösen ords validering av lösen ords skydd i Azure AD ett tomt användar namn för det här scenariot.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Degradering av domänkontrollanten Miss lyckas på grund av ett svagt lokalt administratörs lösen ord

Det finns stöd för degradering av en domänkontrollant som fortfarande kör DC-agentprogram varan. Administratörer bör dock vara medvetna om att DC-agentens program vara fortsätter att genomdriva den aktuella lösen ords principen under degraderingen. Det nya lösen ordet för det lokala administratörs kontot (anges som en del av degraderingen) verifieras som ett annat lösen ord. Microsoft rekommenderar att säkra lösen ord väljs för lokala administratörs konton som en del av en degraderings procedur för domänkontrollanter.

När degraderingen har slutförts och domänkontrollanten har startats om och körs igen som en normal medlems Server, återgår DC-agentens program vara till att köras i ett passivt läge. Den kan sedan avinstalleras när som helst.

## <a name="booting-into-directory-services-repair-mode"></a>Starta i reparations läge för katalog tjänster

Om domänkontrollanten har startats i reparations läge för katalog tjänster identifierar det här tillståndet i DLL-filen för DC-agentens lösen ords filter och gör att alla lösen ords validerings-eller tvångs aktiviteter inaktive ras, oavsett den aktuella aktiva princip konfigurationen. DLL-filen med lösen ords filter för DC-agenten loggar en 10023 varnings händelse i administratörs händelse loggen, till exempel:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Offentlig för hands version av DC Agent-programvara har upphört

Under den offentliga för hands versionen av Azure AD Password Protection var DC-agenten hårdkodad för att sluta bearbeta begär Anden om lösen ords verifiering vid följande datum:

* 1.2.65.0 för version slutar att bearbeta begär Anden om lösen ords verifiering den 1 2019 september.
* Version 1.2.25.0 och tidigare slutade bearbeta begär Anden om lösen ords verifiering den 1 2019 juli.

I takt med att tids gränsen närmar sig, genererar alla tidsbegränsade DC agent-versioner en 10021-händelse i händelse loggen för DC-agenten vid start som ser ut så här:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

När tids gränsen har passerat genererar alla tidsbegränsade DC agent-versioner en 10022-händelse i händelse loggen för DC agent-administratörer vid start som ser ut så här:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Eftersom tids gränsen bara kontrol leras vid den första starten kanske du inte ser dessa händelser förrän du har gått igenom kalenderns tids gräns. När tids gränsen har identifierats kommer inga negativa effekter på antingen domänkontrollanten eller den större miljön att inträffa, förutom att alla lösen ord godkänns automatiskt.

> [!IMPORTANT]
> Microsoft rekommenderar att inaktuella offentliga för hands versioner av DC-agenter omedelbart uppgraderas till den senaste versionen.

Ett enkelt sätt att identifiera DC-agenter i din miljö som måste uppgraderas är genom att köra `Get-AzureADPasswordProtectionDCAgent` cmdleten, till exempel:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

I det här avsnittet är fältet SoftwareVersion självklart vilken nyckel egenskap du ska titta på. Du kan också använda PowerShell-filtrering för att filtrera ut DC-agenter som redan är i eller över den nödvändiga bas linje versionen, till exempel:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD-proxyn för lösen ords skydd är inte tidsbegränsad i någon version. Microsoft rekommenderar fortfarande att både DC-och proxy-agenter uppgraderas till de senaste versionerna när de släpps. `Get-AzureADPasswordProtectionProxy`Cmdleten kan användas för att hitta proxy-agenter som kräver uppgraderingar, ungefär som i exemplet ovan för DC-agenter.

Se [Uppgradera DC-agenten](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) och [Uppgradera proxyservern](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) för mer information om de olika uppgraderings procedurerna.

## <a name="emergency-remediation"></a>Nöd reparation

Om en situation uppstår där DC-agenttjänsten orsakar problem kan DC-agenttjänsten stängas omedelbart. DLL-filen med lösen ords filter för DC-agenten försöker fortfarande anropa tjänsten som inte körs och loggar varnings händelser (10012 10013), men alla inkommande lösen ord godkänns under den tiden. DC-agenttjänsten kan sedan också konfigureras via Windows Service Control Manager med start typen "Disabled" vid behov.

Ett annat åtgärds mått är att ställa in läget aktivera på Nej i Azure AD-portalen för lösen ords skydd. När den uppdaterade principen har laddats ned hamnar varje DC-agenttjänsten i ett quiescent-läge där alla lösen ord godkänns. Mer information finns i [drifts lägen](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Borttagning

Om du bestämmer dig för att avinstallera Azure AD Password Protection-programvaran och rensa alla relaterade tillstånd från domänerna och skogen kan du utföra den här uppgiften med hjälp av följande steg:

> [!IMPORTANT]
> Det är viktigt att utföra de här stegen i rätt ordning. Om en instans av proxy-tjänsten fortfarande körs, återskapas dess serviceConnectionPoint-objekt med jämna mellanrum. Om en instans av DC-agenttjänsten är igång, återskapar den regelbundet sitt serviceConnectionPoint-objekt och SYSVOL-tillstånd.

1. Avinstallera proxy-programvaran från alla datorer. Det här steget kräver **ingen** omstart.
2. Avinstallera DC-agentens program vara från alla domänkontrollanter. Det här steget **kräver** en omstart.
3. Ta manuellt bort alla anslutnings punkter för proxy-tjänsten i varje domän namns kontext. Platsen för dessa objekt kan identifieras med följande Active Directory PowerShell-kommando:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Utelämna inte asterisken ("*") i slutet av $keywords variabel värde.

   Resulterande objekt som hittas via `Get-ADObject` kommandot kan sedan skickas `Remove-ADObject` eller tas bort manuellt.

4. Ta manuellt bort alla anslutnings punkter för DC-agenten i varje domän namns kontext. Det kan finnas ett objekt per domänkontrollant i skogen, beroende på hur mycket program varan distribuerades. Platsen för objektet kan identifieras med följande Active Directory PowerShell-kommando:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Resulterande objekt som hittas via `Get-ADObject` kommandot kan sedan skickas `Remove-ADObject` eller tas bort manuellt.

   Utelämna inte asterisken ("*") i slutet av $keywords variabel värde.

5. Ta bort konfigurations statusen på skogs nivå manuellt. Skogens konfigurations tillstånd underhålls i en behållare i Active Directory namngivnings kontexten för konfigurationen. Du kan identifiera och ta bort den på följande sätt:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ta bort alla SYSVOL-relaterade tillstånd manuellt genom att manuellt ta bort följande mapp och allt dess innehåll:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Vid behov kan den här sökvägen också kommas åt lokalt på en specifik domänkontrollant. standard platsen skulle vara något som liknar följande sökväg:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Den här sökvägen skiljer sig om Sysvol-resursen har kon figurer ATS på en annan plats än standard platsen.

## <a name="health-testing-with-powershell-cmdlets"></a>Hälso testning med PowerShell-cmdletar

AzureADPasswordProtection PowerShell-modulen innehåller två hälso-relaterade cmdlets som utför grundläggande verifiering av att program varan är installerad och fungerar. Det är en bra idé att köra dessa cmdlets när du har konfigurerat en ny distribution, regelbundet och när ett problem har undersökts.

Varje enskilt hälso test returnerar ett Basic-resultat som skickats eller misslyckats, plus ett valfritt meddelande vid fel. I de fall då orsaken till ett fel inte är klar söker du efter fel händelse logg meddelanden som kan förklara felet. Det kan också vara användbart att aktivera text logg meddelanden. Mer information finns i [övervaka lösen ords skydd i Azure AD](howto-password-ban-bad-on-premises-monitor.md).

## <a name="proxy-health-testing"></a>Hälso testning av proxy

Test-AzureADPasswordProtectionProxyHealth-cmdleten stöder två hälsotester som kan köras individuellt. Ett tredje läge gör det möjligt att köra alla tester som inte kräver några parametrar.

### <a name="proxy-registration-verification"></a>Verifiering av proxyserver registrering

Det här testet kontrollerar att proxyagenten är korrekt registrerad i Azure och kan autentisera till Azure. En lyckad körning ser ut så här:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Passed
```

Om ett fel upptäcks returnerar testet ett misslyckat resultat och ett valfritt fel meddelande visas. Här är ett exempel på ett möjligt problem:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Failed No proxy certificates were found - please run the Register-AzureADPasswordProtectionProxy cmdlet to register the proxy.
```

### <a name="proxy-verification-of-end-to-end-azure-connectivity"></a>Proxyautentisering för Azure-anslutning från slut punkt till slut punkt

Det här testet är en supermängd av testet-VerifyProxyRegistration. Det kräver att proxyagenten har registrerats korrekt med Azure, kan autentiseras till Azure och slutligen lägga till en kontroll som innebär att ett meddelande kan skickas till Azure så att fullständig kommunikation från slut punkt till slut punkt fungerar.

En lyckad körning ser ut så här:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyAzureConnectivity

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyAzureConnectivity Passed
```

### <a name="proxy-verification-of-all-tests"></a>Proxyautentisering för alla tester

Det här läget tillåter Mass körning av alla tester som stöds av cmdleten och som inte kräver parameter ingångar. En lyckad körning ser ut så här:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -TestAll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyTLSConfiguration  Passed
VerifyProxyRegistration Passed
VerifyAzureConnectivity Passed
```

## <a name="dc-agent-health-testing"></a>Hälso test för DC-agent

Test-AzureADPasswordProtectionDCAgentHealth cmdlet stöder flera hälso test som kan köras individuellt. Ett tredje läge gör det möjligt att köra alla tester som inte kräver några parametrar.

### <a name="basic-dc-agent-health-tests"></a>Grundläggande hälso test för DC-agent

Följande tester kan köras individuellt och accepteras inte. En kort beskrivning

|Hälso test för DC-agent|Beskrivning|
| --- | :---: |
|-VerifyPasswordFilterDll|Verifierar att DLL-filen för lösen ords filter har lästs in och kan anropa DC-agenttjänsten|
|-VerifyForestRegistration|Verifierar att skogen är registrerad för tillfället|
|-VerifyEncryptionDecryption|Verifierar att grundläggande kryptering och dekryptering fungerar med Microsoft KDS-tjänsten|
|-VerifyDomainIsUsingDFSR|Verifierar att den aktuella domänen använder DFSR för SYSVOL-replikering|
|-VerifyAzureConnectivity|Verifierar slutpunkt-till-slutpunkt-kommunikation med Azure fungerar med hjälp av en tillgänglig proxy|

Här är ett exempel på det-VerifyPasswordFilterDll testet Passing; de andra testerna ser ut ungefär så här:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyPasswordFilterDll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyPasswordFilterDll Passed
```

### <a name="dc-agent-verification-of-all-tests"></a>Kontroll av DC-agenten för alla tester

Det här läget tillåter Mass körning av alla tester som stöds av cmdleten och som inte kräver parameter ingångar. En lyckad körning ser ut så här:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -TestAll

DiagnosticName             Result AdditionalInfo
--------------             ------ --------------
VerifyPasswordFilterDll    Passed
VerifyForestRegistration   Passed
VerifyEncryptionDecryption Passed
VerifyDomainIsUsingDFSR    Passed
VerifyAzureConnectivity    Passed
```

### <a name="connectivity-testing-using-specific-proxy-servers"></a>Anslutnings testning med hjälp av vissa proxyservrar

Många fel söknings situationer innebär att du undersöker nätverks anslutningen mellan DC-agenter och-proxyservrar. Det finns två hälso test som kan fokusera på sådana problem särskilt. Dessa tester kräver att en viss proxyserver anges.

#### <a name="verifying-connectivity-between-a-dc-agent-and-a-specific-proxy"></a>Verifiera anslutningen mellan en DC-agent och en speciell proxy

Det här testet verifierar anslutningen via det första kommunikations benet från DC-agenten till proxyn. Den verifierar att proxyservern tar emot anropet, men ingen kommunikation med Azure är involverad. En lyckad körning ser ut så här:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Passed
```

Här är ett exempel på fel tillstånd där proxy-tjänsten som körs på mål servern har stoppats:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Failed The RPC endpoint mapper on the specified proxy returned no results; please check that the proxy service is running on that server.
```

#### <a name="verifying-connectivity-between-a-dc-agent-and-azure-using-a-specific-proxy"></a>Verifiera anslutningen mellan en DC-agent och Azure (med en speciell proxy)

Det här testet verifierar fullständig anslutning från slut punkt till slut punkt mellan en DC-agent och Azure med hjälp av en speciell proxy. En lyckad körning ser ut så här:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyAzureConnectivityViaSpecificProxy bpl2.bpl.com

DiagnosticName                          Result AdditionalInfo
--------------                          ------ --------------
VerifyAzureConnectivityViaSpecificProxy Passed
```

## <a name="next-steps"></a>Nästa steg

[Vanliga frågor och svar om lösen ords skydd i Azure AD](howto-password-ban-bad-on-premises-faq.md)

Mer information om globala och anpassade listor över blockerade lösen ord finns i artikeln [förbjuda Felaktiga lösen ord](concept-password-ban-bad.md)
