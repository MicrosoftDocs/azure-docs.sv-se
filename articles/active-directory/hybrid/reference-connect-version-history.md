---
title: 'Azure AD Connect: Versionshistorik för | Microsoft Docs'
description: Den här artikeln listar alla versioner av Azure AD Connect och Azure AD Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67bc46b4f612d3d2f377070d5d8280512e0e3df
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576371"
---
# <a name="azure-ad-connect-version-release-history"></a>Versionshistorik för Azure AD Connect
Teamet Azure Active Directory (Azure AD) uppdaterar regelbundet Azure AD Connect med nya funktioner. Alla tillägg gäller inte för alla målgrupper.

Den här artikeln är utformad för att hjälpa dig att hålla reda på de versioner som har släppts och för att förstå vilka ändringar som finns i den senaste versionen.



Den här tabellen är en lista över relaterade ämnen:

Avsnitt |  Information
--------- | --------- |
Steg för att uppgradera från Azure AD Connect | Olika metoder för [att uppgradera från en tidigare version till den senaste](how-to-upgrade-previous-version.md) Azure AD Connect versionen.
Behörigheter som krävs | Information om behörigheter som krävs för att tillämpa en uppdatering finns [i Konton och behörigheter](reference-connect-accounts-permissions.md#upgrade).
Ladda ned| [Ladda ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Att lansera en ny version av Azure AD Connect är en process som kräver flera kvalitetskontroller för att säkerställa tjänstens driftfunktionalitet, och medan vi går igenom den här processen uppdateras versionsnumret för en ny version samt versionsstatusen för att återspegla det senaste tillståndet.
Medan vi går igenom den här processen visas versionsnumret för versionen med "X" i positionen för det lägre versionsnumret, som i "1.3.X.0". Detta anger att viktig information i det här dokumentet är giltig för alla versioner som börjar med "1.3". Så snart vi har slutför lanseringsprocessen uppdateras versionsnumret till den senast utgivna versionen och versionsstatusen uppdateras till "Utgiven för nedladdning och automatisk uppgradering".
Alla versioner av Azure AD Connect kommer inte att göras tillgängliga för automatisk uppgradering. Lanseringsstatusen anger om en version görs tillgänglig för automatisk uppgradering eller endast för nedladdning. Om automatisk uppgradering har aktiverats på Azure AD Connect-servern uppgraderas den servern automatiskt till den senaste versionen Azure AD Connect som släpps för automatisk uppgradering. Observera att inte alla Azure AD Connect konfigurationer är berättigade till automatisk uppgradering. 

För att förtydliga användningen av automatisk uppgradering är den avsedd att skicka alla viktiga uppdateringar och kritiska korrigeringar till dig. Detta är inte nödvändigtvis den senaste versionen eftersom inte alla versioner kräver/inkluderar en korrigering av ett kritiskt säkerhetsproblem (bara ett exempel på många). Ett problem som det skulle åtgärdas med en ny version som tillhandahålls via automatisk uppgradering. Om det inte finns några sådana problem finns det inga uppdateringar som skickas ut med automatisk uppgradering, och i allmänhet bör du vara bra om du använder den senaste versionen av automatisk uppgradering.
Men om du vill ha alla de senaste funktionerna och uppdateringarna är det bästa sättet att se om det finns några att kontrollera den här sidan och installera dem som du vill. 

Följ den här länken om du vill läsa mer om [automatisk uppgradering](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Från och med 1 april 2024 drar vi tillbaka versioner av Azure AD Connect som släpptes före den 1 maj 2018 – version 1.1.751.0 och äldre. 
>
> Du måste se till att du kör en ny version av Azure AD Connect för att få en optimal supportupplevelse. 
>
>Om du kör en tillbakadragen version av Azure AD Connect kanske du inte har de senaste säkerhetskorrigeringarna, prestandaförbättringarna, felsöknings- och diagnostikverktygen och tjänstförbättringarna, och om du behöver support kanske vi inte kan ge dig den servicenivå som din organisation behöver.
>

>
>Läs den här [artikeln om](./how-to-upgrade-previous-version.md) du vill veta mer om hur du uppgraderar Azure AD Connect till den senaste versionen.
>
>Information om versionshistorik om tillbakadragna versioner finns i [Azure AD Connect versionshistorikarkiv](reference-connect-version-history-archive.md)

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> Slutpunkts-API:et för Azure AD Connect V2 är nu tillgängligt i följande Azure-miljöer:
> - Azure Commercial
> - Azure China-moln
> - Azure US Government-moln Det görs inte tillgängligt i Det tyska Azure-molnet

### <a name="release-status"></a>Versionsstatus
2021-03-31: Endast tillgängligt för nedladdning, inte tillgängligt för automatisk uppgradering

### <a name="bug-fixes"></a>Felkorrigeringar
- Den här versionen åtgärdar ett fel i version 1.6.2.4 där Azure AD Connect Health-funktionen inte registrerades korrekt efter uppgraderingen till den versionen och inte fungerade. Kunder som har distribuerat version 1.6.2.4 uppmanas att uppdatera sin Azure AD Connect-server med den här versionen, vilket registrerar hälsofunktionen korrekt. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> Uppdatering per 30 mars 2021: vi har upptäckt ett problem i den här versionen. Hälsotjänsterna registreras inte efter installationen av den här versionen. Vi rekommenderar att du inte installerar den här versionen. Vi kommer att släppa en snabbkorrigering inom kort.
> Om du redan har installerat den här versionen kan du manuellt registrera hälsotjänsterna med hjälp av cmdleten som visas i den [här artikeln](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#manually-register-azure-ad-connect-health-for-sync)

>[!NOTE]
> - Den här versionen görs endast tillgänglig för nedladdning.
> - Uppgraderingen till den här versionen kräver en fullständig synkronisering på grund av ändringar i synkroniseringsregeln.
> - Den här versionen använder som standard AADConnect-servern till den nya V2-startpunkten. Observera att den här punkten inte stöds i det tyska nationella molnet och om [](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) du behöver distribuera den här versionen i den här miljön måste du följa dessa instruktioner för att växla tillbaka till V1-slutpunkten. Om du inte gör det resulterar det i synkroniseringsfel.

### <a name="release-status"></a>Versionsstatus
2021-03-19: Utgiven för nedladdning, inte tillgänglig för automatisk uppgradering

### <a name="functional-changes"></a>Funktionella ändringar

 - Standardregler för synkronisering har uppdaterats för att begränsa medlemskap i tillbakaskrivna grupper till 50 000 medlemmar.
   - Nya standardregler för synkronisering har lagts till för att begränsa antalet medlemskap i tillbakaskrivning av grupp (ut till AD – medlemsgräns för tillbakaskrivning av grupp) och gruppsynkronisering till Azure Active Directory-grupper (utanför AAD – gruppskrivningsmedlemsgräns).
   - Medlemsattribut har lagts till i regeln "Ut till AD – Grupp-SOAInAAD – Exchange" för att begränsa medlemmar i skrivna tillbaka-grupper till 50 000
 - Uppdaterade synkroniseringsregler för att stödja tillbakaskrivning av grupp v2 – Om regeln "In from AAD - Group SOAInAAD" (I från AAD – grupp-SOAInAAD) klonas och AADConnect uppgraderas.
     -Den uppdaterade regeln inaktiveras som standard, så targetWritebackType blir null.
     - AADConnect kommer att tillbakaskrivning av alla molngrupper (inklusive Azure Active Directory säkerhetsgrupper som har aktiverats för tillbakaskrivning) som distributionsgrupper.
   -Om regeln "Ut till AD – grupp-SOAInAAD" klonas och AADConnect uppgraderas.
     - Den uppdaterade regeln inaktiveras som standard. Men en ny synkroniseringsregel "Ut till AD – Grupp SOAInAAD – Exchange" som läggs till kommer att aktiveras.
     - Beroende på den klonade anpassade synkroniseringsregelns prioritet flödar AADConnect attributen Mail och Exchange.
     - Om den klonade anpassade synkroniseringsregeln inte flödar vissa E-post- och Exchange-attribut lägger den nya Exchange Sync-regeln till dessa attribut.
 - Stöd har lagts till för [synkronisering av selektiv lösenordshashar](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - Den nya [cmdleten Single Object Sync har lagts till.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync) Använd den här cmdleten för att Azure AD Connect konfigurationen av synkroniseringen. 
 -  Azure AD Connect stöder nu rollen hybrididentitetsadministratör för att konfigurera tjänsten.
 - AADConnectHealth-agenten har uppdaterats till 3.1.83.0
 - Ny version av [PowerShell-modulen ADSyncTools,](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools)som har flera nya eller förbättrade cmdlets. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Felloggning för tokenförvärvsfel har uppdaterats.
 - Uppdaterade länkarna "Läs mer" på konfigurationssidan för att ge mer information om den länkade informationen.
 - Explicit kolumn har tagits bort från SIDAN CS-sökning i användargränssnittet för gammal synkronisering
 - Ytterligare användargränssnitt har lagts till i flödet för tillbakaskrivning av grupp för att be användaren om autentiseringsuppgifter eller konfigurera sina egna behörigheter med hjälp av ADSyncConfig-modulen om autentiseringsuppgifter inte redan har angetts i ett tidigare steg.
 - Skapa MSA automatiskt för ADSync-tjänstkontot på en domänkontrollant. 
 -  Lade till möjlighet att ange och Azure Active Directory DirSync-funktionsgrupp tillbakaskrivning V2 i befintliga cmdlets:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - Två cmdlets har lagts till för att läsa AWS API-versionen
    - Get-ADSyncAADConnectorImportApiVersion – för att importera AWS API-versionen
    - Get-ADSyncAADConnectorExportApiVersion – för att hämta export av AWS API-version

 - Ändringar som gjorts i synkroniseringsregler spåras nu för att hjälpa till med felsökning av ändringar i tjänsten. Cmdleten "Get-ADSyncRuleAudit" hämtar spårade ändringar.
 - Uppdaterade Add-ADSyncADDSConnectorAccount cmdlet i [PowerShell-modulen ADSyncConfig](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) så att en användare i ADSyncAdmin-gruppen kan ändra AD DS Connector-kontot. 

### <a name="bug-fixes"></a>Felkorrigeringar
 - Inaktiverad förgrundsfärg har uppdaterats för att uppfylla kraven på vit bakgrund. Ytterligare villkor för navigeringsträd har lagts till för att ställa in textfärgen i förgrunden på vit när en inaktiverad sida har valts för att uppfylla kraven på miljö.
 - Öka kornigheten för Set-ADSyncPasswordHashSyncPermissions-cmdlet – Uppdaterat PHS-behörighetsskript (Set-ADSyncPasswordHashSyncPermissions) för att inkludera en valfri "ADobjectDN"-parameter. 
 - Korrigering av tillgänglighetsfel. Skärmläsaren skulle nu beskriva det UX-element som innehåller listan över skogar som "**Skogslista**" i stället för "**Lista över skogar**"
 - Uppdaterade skärmläsarens utdata för vissa objekt i Azure AD Connect guiden. Uppdaterad knapp hovringsfärg för att uppfylla kontrastkraven. Uppdaterade Synchronization Service Manager rubrikfärg för att uppfylla kontrastkraven.
 - Ett problem med att installera AADConnect från en exporterad konfiguration har åtgärdats med anpassade tilläggsattribut – Ett villkor har lagts till för att hoppa över kontrollen av tilläggsattribut i målschemat när synkroniseringsregeln tillämpas.
 - Lämpliga behörigheter läggs till vid installation om funktionen Tillbakaskrivning av grupp är aktiverad.
 - Åtgärda prioritet för duplicerad standardsynkroniseringsregel vid import
 - Ett problem som orsakade ett mellanlagringsfel vid import av V2 API-delta för ett objekt i konflikt som reparerades via hälsoportalen har åtgärdats.
 - Ett problem har åtgärdats i synkroniseringsmotorn som gjorde att CS-objekt hade ett inkonsekvent länktillstånd
 - Importräknare har lagts till för att Get-ADSyncConnectorStatistics utdata.
 - Problem med att välja domän som inte kan nås (valdes tidigare) har åtgärdats i vissa hörnfall under pass2-guiden.
 - Principimport och -export har ändrats så att de misslyckas om den anpassade regeln har duplicerad prioritet 
 - En bugg i logiken för val av domän har åtgärdats.
 - Åtgärdar ett problem med version 1.5.18.0 om du använder mS-DS-ConsistencyGuid som källankare och har klonat regeln In från AD – Gruppkoppling.
 - Fresh AADConnect-installationer använder tröskelvärdet för exportborttagning som lagras i molnet om det finns en tillgänglig och det inte finns någon annan som skickas.
 - Ett problem har åtgärdats där AADConnect inte läser AD displayName-ändringar av hybridanslutna enheter

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Versionsstatus
2020-07-29: Publicerat för nedladdning

### <a name="functional-changes"></a>Funktionella ändringar
Det här är en felkorrigering. Det finns inga funktionsändringar i den här versionen.

### <a name="fixed-issues"></a>Åtgärdade problem

- Ett problem har åtgärdats där administratören inte kan aktivera "sömlös enkel inloggning" om AZUREADSSOACC-datorkontot redan finns i "Active Directory".
- Ett problem som orsakade ett mellanlagringsfel under V2 API-deltaimporten för ett objekt i konflikt som reparerades via hälsoportalen har åtgärdats.
- Åtgärdat ett problem i import/export-konfigurationen där inaktiverad anpassad regel importerades som aktiverad.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Versionsstatus
2020-07-10: Publicerat för nedladdning

### <a name="functional-changes"></a>Funktionella ändringar
Den här versionen innehåller en offentlig förhandsversion av funktionen för att exportera konfigurationen av en befintlig Azure AD Connect-server till en . JSON-fil som sedan kan användas när du installerar en Azure AD Connect server för att skapa en kopia av den ursprungliga servern.

En detaljerad beskrivning av den här nya funktionen finns i den [här artikeln](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Åtgärdade problem
- Åtgärdat en bugg där det skulle finnas en falsk varning om den lokala DB-storleken på de lokaliserade versionerna under uppgraderingen.
- En bugg har åtgärdats där det uppstod ett falskt-fel i apphändelserna för byte av kontonamn/domännamn.
- Åtgärdat ett fel där Azure AD Connect kunde inte installeras på en domänkontrollant, vilket gav felet "medlemmen hittades inte".


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Versionsstatus
2020-05-07: Publicerat för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
Den här snabbkorrigeringsbygget åtgärdar ett problem där omarkerade domäner väljs felaktigt från guidens användargränssnitt om endast grandchild-containrar har valts.


>[!NOTE]
>Den här versionen innehåller den nya Azure AD Connect V2-slutpunkts-API:et.  Den här nya V2-slutpunkten är för närvarande i offentlig förhandsversion.  Den här versionen eller senare krävs för att använda det nya V2-slutpunkts-API:et.  Att bara installera den här versionen aktiverar dock inte V2-slutpunkten. Du fortsätter att använda V1-slutpunkten om du inte aktiverar V2-slutpunkten.  Du måste följa stegen under Azure AD Connect synkronisera [V2-slutpunkts-API :et (offentlig förhandsversion)](how-to-connect-sync-endpoint-api-v2.md) för att aktivera det och välja den offentliga förhandsversionen.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Versionsstatus
2020-04-23: Utgiven för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
Den här snabbkorrigeringen åtgärdar ett problem som introducerades i version 1.5.20.0 där en innehavaradministratör med MFA inte kunde aktivera DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Versionsstatus
2020-04-20: Publicerat för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
Den här snabbkorrigeringsbygget åtgärdar ett problem i version 1.5.20.0 om du har klonat regeln In från **AD –** Gruppkoppling och inte har klonat regeln I från AD **–** Grupp common.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Versionsstatus
2020-04-09: Publicerat för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
- Den här snabbkorrigeringsbygget åtgärdar ett problem med version 1.5.18.0 om du har funktionen Gruppfiltrering aktiverad och använder mS-DS-ConsistencyGuid som källfästpunkt.
- Ett problem har åtgärdats i PowerShell-modulen ADSyncConfig, där anrop av DSACLS-kommando som används i alla cmdlets för Set-ADSync* Behörigheter skulle orsaka något av följande fel:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Om du har klonat synkroniseringsregeln In från **AD –** Gruppkoppling och inte har klonat synkroniseringsregeln In från **AD –** Grupp common och planerar att uppgradera, slutför du följande steg som en del av uppgraderingen:
> 1. Under Uppgradering avmarkerar du alternativet **Starta synkroniseringsprocessen när konfigurationen är klar.**
> 2. Redigera synkroniseringsregeln för klonad koppling och lägg till följande två transformningar:
>     - Ange direktflöde `objectGUID` till `sourceAnchorBinary` .
>     - Ange uttrycksflödet `ConvertToBase64([objectGUID])` till `sourceAnchor` .     
> 3. Aktivera schemaläggaren med hjälp av `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Versionsstatus
2020-04-02: Publicerat för nedladdning

### <a name="functional-changes-adsyncautoupgrade"></a>Funktionella ändringar ADSyncAutoUpgrade 

- Stöd har lagts till för funktionen mS-DS-ConsistencyGuid för gruppobjekt. På så sätt kan du flytta grupper mellan skogar eller återansluta grupper i AD till Azure AD där AD-gruppen objectID har ändrats, t.ex. när en AD-server återskapas efter en katastrof. Mer information finns i [Flytta grupper mellan skogar.](how-to-connect-migrate-groups.md)
- Attributet mS-DS-ConsistencyGuid anges automatiskt för alla synkroniserade grupper och du behöver inte göra något för att aktivera den här funktionen. 
- Tog bort Get-ADSyncRunProfile eftersom den inte längre används. 
- Den varning som visas när du försöker använda ett företagsadministratörs- eller domänadministratörskonto för AD DS-anslutningskontot har ändrats för att ge mer kontext. 
- En ny cmdlet har lagts till för att ta bort objekt från anslutningsappens utrymme som det gamla CSDelete.exe-verktyget har tagits bort och ersätts med den nya Remove-ADSyncCSObject cmdleten. Den Remove-ADSyncCSObject cmdleten tar ett CsObject som indata. Det här objektet kan hämtas med hjälp av Get-ADSyncCSObject cmdlet.

>[!NOTE]
>Det gamla CSDelete.exe verktyget har tagits bort och ersatts med den nya Remove-ADSyncCSObject cmdleten 

### <a name="fixed-issues"></a>Åtgärdade problem

- En bugg i skogs-/OU-väljaren för tillbakaskrivning av grupp när guiden Azure AD Connect har inaktiverats har åtgärdats. 
- En ny felsida har introducerats som visas om de nödvändiga DCOM-registervärdena saknas med en ny hjälplänk. Informationen skrivs också till loggfiler. 
- Ett problem med att skapa Azure Active Directory-synkroniseringskontot har åtgärdats där aktivering av katalogtillägg eller PHS kan misslyckas eftersom kontot inte har spridits över alla tjänstrepliker innan användningsförsök gjordes. 
- En bugg i komprimeringsverktyget för synkroniseringsfel som inte hanterade surrogattecken korrekt har åtgärdats. 
- Åtgärdat en bugg i den automatiska uppgraderingen som gjorde att servern pausade servern i pausläge. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Versionsstatus
2019-12-09: Version för nedladdning. Inte tillgängligt via automatisk uppgradering.
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
- Vi har uppdaterat synkronisering av lösenordshashar för Azure AD Domain Services att korrekt ta hänsyn till utfyllnad i Kerberos-hash-värden.  Detta ger en prestandaförbättring vid lösenordssynkronisering från Azure AD till Azure AD Domain Services.
- Vi har lagt till stöd för tillförlitliga sessioner mellan autentiseringsagenten och Service Bus.
- Den här versionen tillämpar TLS 1.2 för kommunikation mellan autentiseringsagenten och molntjänster.
- Vi har lagt till en DNS-cache för websocket-anslutningar mellan autentiseringsagenten och molntjänster.
- Vi har lagt till möjligheten att rikta in en specifik agent från molnet för att testa agentanslutningen.

### <a name="fixed-issues"></a>Åtgärdade problem
- Version 1.4.18.0 hade en bugg där PowerShell-cmdleten för DSSO använde inloggningsfönsterautentiseringsuppgifterna i stället för de administratörsautentiseringsuppgifter som angavs när ps kördes. Därför gick det inte att aktivera DSSO i flera skogar via Azure AD Connect användargränssnittet. 
- En korrigering har gjorts för att aktivera DSSO samtidigt i hela skogen Azure AD Connect användargränssnittet

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Versionsstatus
2019-11-08: Publicerat för nedladdning. Inte tillgängligt via automatisk uppgradering.

>[!IMPORTANT]
>På grund av en intern schemaändring i den här versionen av Azure AD Connect måste du uppdatera MSOnline PowerShell-modulen till version 1.1.1.183.57 eller senare om du hanterar konfigurationsinställningar för AD FS-förtroenderelationer med MSOnline PowerShell

### <a name="fixed-issues"></a>Åtgärdade problem

Den här versionen åtgärdar ett problem med befintliga Hybrid Azure AD-anslutna enheter. Den här versionen innehåller en ny enhetssynkroniseringsregel som korrigerar det här problemet.
Observera att den här regeländringen kan orsaka borttagning av föråldrade enheter från Azure AD. Detta är inget problem eftersom dessa enhetsobjekt inte används av Azure AD under auktoriseringen för villkorsstyrd åtkomst. För vissa kunder kan antalet enheter som tas bort via den här regeländringen överskrida tröskelvärdet för borttagning. Om du ser att borttagningen av enhetsobjekt i Azure AD överskrider tröskelvärdet för borttagning av export rekommenderar vi att du tillåter att borttagningarna går igenom. [Så här tillåter du att borttagningar flödar när de överskrider tröskelvärdet för borttagning](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Versionsstatus
2019-09-28: Publicerat för automatisk uppgradering för att välja klienter. Inte tillgängligt för nedladdning.

Den här versionen åtgärdar ett fel där vissa servrar som uppgraderades automatiskt från en tidigare version till 1.4.18.0 och hade problem med självbetjäning av lösenordsåterställning (SSPR) och tillbakaskrivning av lösenord.

### <a name="fixed-issues"></a>Åtgärdade problem

Under vissa omständigheter återaktiverade inte servrar som uppgraderades automatiskt till version 1.4.18.0 självbetjäning av lösenordsåterställning och tillbakaskrivning av lösenord efter uppgraderingen. Den här versionen av automatisk uppgradering åtgärdar problemet och återaktiverar självbetjäning av lösenordsåterställning och tillbakaskrivning av lösenord.

Vi har åtgärdat en bugg i komprimeringsverktyget för synkroniseringsfel som inte hanterade surrogattecken korrekt.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Vi undersöker en incident där vissa kunder har problem med befintliga Hybrid Azure AD-anslutna enheter efter uppgraderingen till den här versionen av Azure AD Connect. Vi rekommenderar kunder som har distribuerat Hybrid Azure AD-anslutning att skjuta upp uppgraderingen till den här versionen tills rotorsaken till dessa problem är helt förstådd och åtgärdad. Mer information kommer att tillhandahållas så snart som möjligt.

>[!IMPORTANT]
>Med den här Azure AD Connect kan vissa kunder se att vissa eller alla sina Windows-enheter försvinner från Azure AD. Det här är inget problem eftersom dessa enhetsidentiteter inte används av Azure AD under auktoriseringen för villkorsstyrd åtkomst. Mer information finns i [Understanding Azure AD Connect 1.4.xx.x device disappearnce (Förstå hur en 1.4.xx.x-enhet försvinner)](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Versionsstatus
2019-09-25: Har endast släppts för automatisk uppgradering.

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
- Nya felsökningsverktyg hjälper till att felsöka scenarier där "användaren inte synkroniserar", "gruppen synkroniseras inte" eller "gruppmedlem synkroniseras inte".
- Lägg till stöd för nationella moln i Azure AD Connect felsökningsskript.
- Kunder bör informeras om att de föråldrade WMI-slutpunkterna för MIIS_Service nu har tagits bort. Alla WMI-åtgärder bör nu göras via PS-cmdlets.
- Säkerhetsförbättring genom att återställa begränsad delegering för AZUREADSSOACC-objekt.
- När du lägger till/redigerar en synkroniseringsregel och det finns några attribut som används i regeln som finns i anslutningsschemat men som inte läggs till i anslutningsprogrammet, läggs attributen automatiskt till i anslutningsprogrammet. Samma sak gäller för objekttypen som regeln påverkar. Om något läggs till i anslutningsappen markeras anslutningsappen för fullständig import vid nästa synkroniseringscykel.
- Användning av en företags- eller domänadministratör som anslutningskonto stöds inte längre i nya Azure AD Connect distributioner. Aktuella Azure AD Connect-distributioner som använder en företags- eller domänadministratör som anslutningskonto påverkas inte av den här versionen.
- I Synkroniseringshanteraren körs en fullständig synkronisering när regeln skapas/redigeras/tas bort. Ett popup-fönster visas vid alla regeländringar som meddelar användaren om fullständig import eller fullständig synkronisering kommer att köras.
- Åtgärdssteg för lösenordsfel har lagts till på sidan > anslutningsappar > anslutningsappen.
- En utfasningsvarning för Synkroniseringstjänsthanteraren har lagts till på egenskapssidan för anslutningsappen. Den här varningen meddelar användaren om att ändringar ska göras via Azure AD Connect guiden.
- Ett nytt fel har lagts till för problem med en användares lösenordsprincip.
- Förhindra felaktig konfiguration av gruppfiltrering efter domän- och OU-filter. Gruppfiltrering visar ett fel när domänen/organisationsenheten för den angivna gruppen redan har filtrerats bort och håller användaren från att gå vidare tills problemet har lösts.
- Användarna kan inte längre skapa en anslutningsapp för Active Directory Domain Services eller Windows Azure Active Directory i Synchronization Service Manager användargränssnitt.
- Tillgängligheten för anpassade ui-kontroller i Synchronization Service Manager har åtgärdats.
- Aktiverade sex federationshanteringsuppgifter för alla inloggningsmetoder i Azure AD Connect.  (Tidigare var endast uppgiften "Uppdatera AD FS TLS/SSL-certifikat" tillgänglig för alla inloggningar.)
- En varning har lagts till när inloggningsmetoden ändras från federation till PHS eller PTA om att alla Azure AD-domäner och -användare konverteras till hanterad autentisering.
- Tog bort certifikat för tokensignering från aktiviteten "Återställ Azure AD och AD FS förtroende" och lade till en separat underaktivitet för att uppdatera dessa certifikat.
- Lade till en ny federationshanteringsaktivitet som kallas "Hantera certifikat" som har underordnade uppgifter för att uppdatera TLS- eller tokensigneringscertifikat för AD FS servergrupp.
- Lade till en ny federationshanteringsunderaktivitet med namnet "Ange primär server" som gör att administratörer kan ange en ny primär server för AD FS servergrupp.
- Lade till en ny federationshanteringsaktivitet som kallas "Hantera servrar" som har underuppgifter för att distribuera en AD FS-server, distribuera en Programproxy-server och ange primär server.
- En ny federationshanteringsaktivitet med namnet "Visa federationskonfiguration" har lagts till som visar de AD FS inställningarna.  (På grund av det här AD FS har inställningarna tagits bort från sidan Granska din lösning.)

### <a name="fixed-issues"></a>Åtgärdade problem
- Problem med synkroniseringsfel har åtgärdats i scenariot där ett användarobjekt som tar över motsvarande kontaktobjekt har en självreferens (t.ex. användaren är sin egen chef).
- Hjälp-popup-fönster visas nu i fokus på tangentbordet.
- För Automatisk uppgradering, om någon app i konflikt körs från 6 timmar, tar du bort den och fortsätter med uppgraderingen.
- Begränsa antalet attribut som en kund kan välja till 100 per objekt när du väljer katalogtillägg. Detta förhindrar att felet uppstår under exporten eftersom Azure har högst 100 tilläggsattribut per objekt.
- En bugg har åtgärdats som gjorde AD-anslutningsskriptet mer robust.
- En bugg har åtgärdats för att Azure AD Connect installera på en dator med hjälp av en befintlig WCF-tjänst med namngivna pipes.
- Förbättrad diagnostik och felsökning kring grupprinciper som inte tillåter att ADSync-tjänsten startar när den först installeras.
- En bugg där visningsnamnet för en Windows-dator har skrivits felaktigt har åtgärdats.
- Åtgärda ett fel där operativsystemtypen för en Windows-dator har skrivits felaktigt.
- En bugg där icke-Windows 10 datorer synkroniserades oväntat har åtgärdats. Observera att effekten av den här ändringen är att datorer som inte är Windows 10-datorer som tidigare har synkroniserats nu tas bort. Detta påverkar inte några funktioner eftersom synkroniseringen av Windows-datorer endast används för Hybrid Azure AD-domänanslutningar, som endast fungerar för Windows-10-enheter.
- Flera nya (interna) cmdlets har lagts till i ADSync PowerShell-modulen.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Det finns ett känt problem med att uppgradera Azure AD Connect från en tidigare version till 1.3.21.0 där Microsoft 365-portalen inte återspeglar den uppdaterade versionen även om Azure AD Connect har uppgraderats.
>
> För att lösa detta måste du importera **AdSync-modulen** och sedan köra `Set-ADSyncDirSyncConfiguration` PowerShell-cmdleten på Azure AD Connect servern.  Du kan använda följande steg:
>
>1. Öppna PowerShell i administratörsläge.
>2. Kör `Import-Module "ADSync"`.
>3. Kör `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Versionsstatus 

2019-05-14: Publicerat för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem 

- En säkerhetsrisk för utökade privilegier som finns i Microsoft Azure Active Directory Connect version 1.3.20.0 har åtgärdats.  Den här säkerhetsrisken kan under vissa omständigheter tillåta att en angripare kör två PowerShell-cmdlets i kontexten för ett privilegierat konto och utför privilegierade åtgärder.  Den här säkerhetsuppdateringen åtgärdar problemet genom att inaktivera dessa cmdlets. Mer information finns i [säkerhetsuppdatering.](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)


## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
