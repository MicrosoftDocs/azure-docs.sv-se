---
title: 'Azure AD Connect: versions historik | Microsoft Docs'
description: Den här artikeln innehåller en lista över alla versioner av Azure AD Connect och Azure AD Sync.
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
ms.openlocfilehash: 71eaccd615f5c3460e7b48b2e92d5f874c8de12e
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772118"
---
# <a name="azure-ad-connect-version-release-history"></a>Versionshistorik för Azure AD Connect
Gruppen Azure Active Directory (Azure AD) uppdaterar regelbundet Azure AD Connect med nya funktioner. Alla tillägg gäller inte för alla mål grupper.

Den här artikeln är utformad för att hjälpa dig att hålla koll på de versioner som har släppts och för att förstå vad ändringarna är i den senaste versionen.



Den här tabellen är en lista över närliggande ämnen:

Avsnitt |  Information
--------- | --------- |
Steg för att uppgradera från Azure AD Connect | Olika metoder för att [Uppgradera från en tidigare version till den senaste versionen av](how-to-upgrade-previous-version.md) Azure AD Connect.
Behörigheter som krävs | För behörigheter som krävs för att tillämpa en uppdatering, se [konton och behörigheter](reference-connect-accounts-permissions.md#upgrade).
Ladda ned| [Ladda ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Att släppa en ny version av Azure AD Connect är en process som kräver flera kvalitets kontroll steg för att säkerställa drifts funktionerna i tjänsten, och medan vi går igenom den här processen kommer versions numret för en ny version och versions statusen att uppdateras för att avspegla det senaste tillståndet.
Medan vi går igenom den här processen visas versions numret för versionen med ett "X" på den lägre versions nummer positionen, som i "1.3. X. 0" – Detta anger att viktig information i det här dokumentet är giltig för alla versioner som börjar med "1,3.". Så snart som vi har slutfört versions processen uppdateras versions numret till den senast utgivna versionen och versions statusen uppdateras till "lanseras för hämtning och automatisk uppgradering".
Det är inte alla versioner av Azure AD Connect som görs tillgängliga för automatisk uppgradering. Versions statusen anger om en version görs tillgänglig för automatisk uppgradering eller endast för hämtning. Om automatisk uppgradering har Aktiver ATS på Azure AD Connect-servern, uppgraderas servern automatiskt till den senaste versionen av Azure AD Connect som släpps för automatisk uppgradering. Observera att inte alla Azure AD Connect konfigurationer är berättigade till automatisk uppgradering. 

För att klargöra användningen av automatisk uppgradering är det tänkt att skicka alla viktiga uppdateringar och viktiga korrigeringar till dig. Detta är inte nödvändigt vis den senaste versionen eftersom inte alla versioner kommer att kräva/inkludera en korrigering av ett kritiskt säkerhets problem (bara ett exempel av många). Ett problem som kan åtgärdas med en ny version som tillhandahålls via automatisk uppgradering. Om det inte finns några sådana problem finns det inga uppdateringar som pushas med automatisk uppgradering, och i allmänhet om du använder den senaste versionen av automatisk uppgradering.
Men om du vill ha alla de senaste funktionerna och uppdateringarna är det bästa sättet att se om det finns några att kontrol lera den här sidan och installera dem när du ser anpassa. 

Använd den här länken för att läsa mer om [automatisk uppgradering](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Från och med den 1 april 2024 kommer vi att dra tillbaka versioner av Azure AD Connect som släpptes före den 1 maj 2018-version 1.1.751.0 och äldre. 
>
> Du måste kontrol lera att du kör en ny version av Azure AD Connect för att få en optimal support upplevelse. 
>
>Om du kör en äldre version av Azure AD Connect kanske du inte har de senaste säkerhets korrigeringarna, prestanda förbättringar, fel sökning och diagnostiska verktyg och förbättringar av tjänster, och om du behöver support kanske vi inte kan tillhandahålla den nivå av tjänst som din organisation behöver.
>

>
>Mer information om hur du uppgraderar Azure AD Connect till den senaste versionen finns i [den här artikeln](./how-to-upgrade-previous-version.md) .
>
>Information om versions historiken på återkallade versioner finns i [Azure AD Connect versions historik Arkiv](reference-connect-version-history-archive.md)


## <a name="1624"></a>1.6.2.4

>[!NOTE]
> - Den här versionen kommer endast att göras tillgänglig för hämtning.
> - Uppgraderingen till den här versionen kräver en fullständig synkronisering på grund av ändringar i synkroniseringsregeln.
> - Den här versionen är som standard AADConnect-servern för den nya v2-slut punkten. Observera att den här slut punkten inte stöds i det tyska nationella molnet, det kinesiska nationella molnet och det amerikanska myndighets molnet och om du behöver distribuera den här versionen i dessa moln måste du följa [dessa instruktioner](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) för att gå tillbaka till v1-slutpunkten. Om du inte gör det leder det till att det uppstår fel i synkroniseringen.

### <a name="release-status"></a>Versionsstatus
3/19/2021: har släppts för nedladdning

### <a name="functional-changes"></a>Funktionella ändringar

 - Uppdaterade standard regler för synkronisering för att begränsa medlemskap i de skrivna grupperna till 50 000-medlemmar.
   - Nya standard regler för synkronisering har lagts till för att begränsa antalet medlemskap i tillbakaskrivning av grupp (ut till AD-grupps medlems gräns för tillbakaskrivning) och gruppsynkronisering till Azure Active Directory (ut till AAD-grupp writeup medlems gräns) grupper.
   - Attributet member till regeln "out till AD-Group SOAInAAD-Exchange" har lagts till för att begränsa medlemmar i de skrivna grupperna till 50 000
 - Uppdaterade formateringsregler till stöd för tillbakaskrivning av grupp v2 – om regeln "i från AAD-Group SOAInAAD" är klonad och AADConnect uppgraderas.
     -Den uppdaterade regeln kommer att inaktive ras som standard och targetWritebackType blir null.
     - AADConnect kommer att ångra alla moln grupper (inklusive Azure Active Directory säkerhets grupper som har Aktiver ATS för tillbakaskrivning) som distributions grupper.
   – Om regeln "ut till AD-Group SOAInAAD" är klonad och AADConnect uppgraderas.
     - Den uppdaterade regeln är inaktive rad som standard. En ny Synkroniseringsregel "ut till AD-Group SOAInAAD-Exchange" som läggs till aktive ras dock.
     - Beroende på prioriteten för den klonade anpassade synkroniseringsregeln kommer AADConnect att flöda e-postmeddelandet och Exchange-attributen.
     - Om den klonade anpassade synkroniseringsregeln inte flödar för några e-post-och Exchange-attribut, lägger den nya Exchange Sync-regeln till dessa attribut.
 - Stöd har lagts till för [Lösenordssynkronisering för selektivt lösen ord](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - Den nya [cmdleten för synkronisering av en enda objekt](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync)har lagts till. Använd denna cmdlet för att felsöka din Azure AD Connect Sync-konfiguration. 
 - Uppdaterade AADConnectHealth-agenten till 3.1.83.0
 - Ny version av [ADSyncTools PowerShell-modulen](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools), som har flera nya eller förbättrade cmdletar. 
 
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

 - Uppdaterad fel loggning för fel vid hämtning av token.
 - Uppdaterade Länkar för "Läs mer" på konfigurations sidan för att ge mer information om den länkade informationen.
 - Den explicita kolumnen togs bort från sidan CS-sökning i det gamla användar gränssnittet för synkronisering
 - Ytterligare användar gränssnitt har lagts till i tillbakaskrivning av grupp för att uppmana användaren att ange autentiseringsuppgifter eller konfigurera sina egna behörigheter med ADSyncConfig-modulen om autentiseringsuppgifterna inte redan har angetts i ett tidigare steg.
 - Skapa MSA automatiskt för ADSync-tjänstkontot på en DOMÄNKONTROLLANT. 
 -  Möjlighet att ställa in och hämta Azure Active Directory DirSync-funktionen tillbakaskrivning v2 i befintliga cmdlet: ar har lagts till:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - 2 cmdletar har lagts till för att läsa AWS API-version
    - Get-ADSyncAADConnectorImportApiVersion – Hämta API-version för import AWS
    - Get-ADSyncAADConnectorExportApiVersion-för att hämta API-version för export AWS

 - Ändringar som görs i regler för synkronisering spåras nu för att hjälpa till att felsöka ändringar i tjänsten. Cmdleten "Get-ADSyncRuleAudit" kommer att hämta spårade ändringar.
 - Uppdaterade Add-ADSyncADDSConnectorAccount-cmdlet: en i [ADSyncConfig PowerShell-modulen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) så att en användare i ADSyncAdmin-gruppen kan ändra AD DS-anslutningsprogrammet. 

### <a name="bug-fixes"></a>Felkorrigeringar
 - Uppdaterade inaktiverade förgrunds färger för att uppfylla ljus styrkas kraven på en vit bakgrund. Lade till ytterligare villkor för navigerings trädet för att ange förgrunds text färg till vit när en inaktive rad sida har valts för att uppfylla luminanss krav.
 - Öka granularitet för Set-ADSyncPasswordHashSyncPermissions cmdlet – uppdaterade PHS-behörigheter skript (Set-ADSyncPasswordHashSyncPermissions) för att inkludera en valfri "ADobjectDN"-parameter. 
 - Hjälpmedels fel korrigering. Skärm läsaren skulle nu beskriva det UX-element som innehåller listan över skogar som "skogs **lista**" i stället för **"lista över skogar"**
 - Uppdaterad skärm läsar utdata för vissa objekt i guiden Azure AD Connect. Uppdaterad knapp hov rings färg för att uppfylla kontrast kraven. Uppdaterad Synchronization Service Manager rubrik färg för att uppfylla kontrast kraven.
 - Ett problem har åtgärd ATS vid installation av AADConnect från exporterad konfiguration med anpassade tilläggs-attribut – ett villkor har lagts till för att hoppa över kontroll av attribut för tillägg i mål schema när synkroniseringsregeln tillämpades.
 - Lämpliga behörigheter läggs till vid installationen om funktionen för tillbakaskrivning av grupp är aktive rad.
 - Korrigera dubbletter av regel prioritet för synkronisering vid import
 - Ett problem har åtgärd ATS som orsakade ett mellanlagrings fel under v2-API delta import för ett objekt som har reparerats via hälso portalen.
 - Ett problem har åtgärd ATS i Synkroniseringsmotorn som gjorde att CS-objekt har ett inkonsekvent länk tillstånd
 - Import räknare har lagts till i Get-ADSyncConnectorStatistics utdata.
 - Åtgärdad domän som inte kan frigöras och som marker ATS tidigare (vald tidigare) i vissa hörn fall under guiden pass2.
 - Den ändrade princip importen och exporten misslyckades om den anpassade regeln har en dubblett-prioritet 
 - Ett fel har åtgärd ATS i logiken för val av domän.
 - Åtgärdar ett problem med build-1.5.18.0 om du använder mS-DS-ConsistencyGuid som käll ankare och har klonat in från AD-Groups kopplings regel.
 - De nya AADConnect-installationerna använder tröskelvärdet för borttagning av export som lagras i molnet om det finns något tillgängligt och det inte finns någon annan som har skickats in.
 - Åtgärdat problem där AADConnect inte läser AD displayName-ändringar i hybrid-anslutna enheter

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Versionsstatus
07/29/2020: har släppts för nedladdning

### <a name="functional-changes"></a>Funktionella ändringar
Detta är en version av fel korrigering. Det finns inga funktionella ändringar i den här versionen.

### <a name="fixed-issues"></a>Åtgärdade problem

- Ett problem där administratören inte kan aktivera "sömlös enkel inloggning" har åtgärd ATS om AZUREADSSOACC dator konto redan finns i "Active Directory".
- Ett problem har åtgärd ATS som orsakade ett mellanlagrings fel under v2-API delta import för ett objekt som har reparerats via hälso portalen.
- Ett problem har åtgärd ATS i import/export-konfigurationen där inaktiverade anpassad regel har importer ATS som aktive rad.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Versionsstatus
07/10/2020: har släppts för nedladdning

### <a name="functional-changes"></a>Funktionella ändringar
Den här versionen innehåller en offentlig för hands version av funktionerna för att exportera konfigurationen av en befintlig Azure AD Connect-Server till en. JSON-fil som sedan kan användas när du installerar en ny Azure AD Connect-Server för att skapa en kopia av den ursprungliga servern.

En detaljerad beskrivning av den här nya funktionen finns i [den här artikeln](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Åtgärdade problem
- En bugg har åtgärd ATS där det skulle finnas en falsk varning om den lokala databas storleken på lokaliserade versioner under uppgraderingen.
- En bugg har åtgärd ATS där det skulle finnas ett falskt fel i app-händelserna för konto namnet/domän namns växlingen.
- Ett fel inträffade där Azure AD Connect inte gick att installera på en DOMÄNKONTROLLANT, vilket ger fel meddelandet "medlemmen hittades inte".


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Versionsstatus
05/07/2020: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
Den här snabb korrigeringen åtgärdar ett problem där omarkerade domäner har marker ATS felaktigt i användar gränssnittet i guiden om endast grandchild-behållare valdes.


>[!NOTE]
>Den här versionen innehåller det nya Azure AD Connect Sync v2 Endpoint API.  Den nya v2-slutpunkten är för närvarande en offentlig för hands version.  Den här versionen eller senare krävs för att använda det nya v2-slutpunkt-API: et.  Men om du bara installerar den här versionen aktive ras inte v2-slutpunkten. Du kommer att fortsätta att använda v1-slutpunkten om du inte aktiverar v2-slutpunkten.  Du måste följa stegen under [Azure AD Connect Sync v2-slutpunkt-API (offentlig för hands version)](how-to-connect-sync-endpoint-api-v2.md) för att aktivera den och välja den offentliga för hands versionen.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Versionsstatus
04/23/2020: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
Den här snabb korrigeringen åtgärdar ett problem som introducerades i build-1.5.20.0, där en innehavaradministratör med MFA inte kunde aktivera DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Versionsstatus
04/20/2020: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
Den här snabb korrigeringen åtgärdar ett problem i build-1.5.20.0 om du har klonat **i kopplings regeln för AD-grupp** och inte har klonat **in i den gemensamma regeln för AD-gruppen** .

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Versionsstatus
04/09/2020: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
- Den här snabb korrigeringen löser ett problem med build-1.5.18.0 om du har grupp filtrerings funktionen aktive rad och använder mS-DS-ConsistencyGuid som käll ankare.
- Ett problem har åtgärd ATS i ADSyncConfig PowerShell-modulen, där DSACLS-kommandot som används i alla cmdletar för set-ADSync *-behörighet skulle orsaka något av följande fel:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Om du har klonat in från regeln för att **ansluta till AD-gruppen** och inte har klonat **in från regel för vanlig synkronisering i AD-gruppen** och planerar att uppgradera, slutför du följande steg som en del av uppgraderingen:
> 1. Under uppgraderingen avmarkerar du alternativet **starta synkroniseringsprocessen när konfigurationen är klar**.
> 2. Redigera synkroniseringsregeln för klonad koppling och Lägg till följande två omvandlingar:
>     - Ange direkt flöde `objectGUID` till `sourceAnchorBinary` .
>     - Ange uttrycks flöde `ConvertToBase64([objectGUID])` till `sourceAnchor` .     
> 3. Aktivera Scheduler med hjälp av `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Versionsstatus
04/02/2020: har släppts för nedladdning

### <a name="functional-changes-adsyncautoupgrade"></a>Funktionella ändringar ADSyncAutoUpgrade 

- Stöd har lagts till för mS-DS-ConsistencyGuid-funktionen för grupp objekt. På så sätt kan du flytta grupper mellan skogar eller återansluta grupper i AD till Azure AD där AD-gruppen objectID har ändrats, t. ex. När en AD-server återskapas efter en Calamity. Mer information finns i [flytta grupper mellan skogar](how-to-connect-migrate-groups.md).
- Attributet mS-DS-ConsistencyGuid anges automatiskt för alla synkroniserade grupper och du behöver inte göra något för att aktivera den här funktionen. 
- Get-ADSyncRunProfile har tagits bort eftersom den inte längre används. 
- Ändrade varningen som visas när du försöker att använda ett företags administratörs konto eller ett domän administratörs konto för AD DS-anslutningsprogrammet för att tillhandahålla mer kontext. 
- En ny cmdlet har lagts till för att ta bort objekt från anslutnings området det gamla CSDelete.exe verktyget tas bort och ersätts med den nya Remove-ADSyncCSObject-cmdleten. Remove-ADSyncCSObject-cmdleten tar en CsObject som inmatad. Det här objektet kan hämtas med hjälp av Get-ADSyncCSObject-cmdleten.

>[!NOTE]
>Det gamla CSDelete.exes verktyget har tagits bort och ersatts med den nya Remove-ADSyncCSObject-cmdleten 

### <a name="fixed-issues"></a>Åtgärdade problem

- En bugg har åtgärd ATS i gruppen tillbakaskrivning skog/OU för att köra guiden Azure AD Connect igen när funktionen har inaktiverats. 
- Introducerade en ny felsida som ska visas om de nödvändiga DCOM-registervärdena saknas med en ny hjälp länk. Information skrivs också till loggfiler. 
- Ett problem har åtgärd ATS när Azure Active Directory Sync-kontot som aktiverar katalog tillägg eller PHS Miss lyckas på grund av att kontot inte har spridits över alla tjänst repliker före försöket att använda. 
- Ett fel har åtgärd ATS i komprimerings verktyget för synkroniseringsfel som inte hanterade surrogat tecken korrekt. 
- En bugg har åtgärd ATS i den automatiska uppgradering som gjorde att servern avbröts av Scheduler. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Versionsstatus
12/9/2019: version för hämtning. Inte tillgängligt via automatisk uppgradering.
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
- Vi uppdaterade lösen ordets hash-synkronisering för Azure AD Domain Services till rätt konto för utfyllnad i Kerberos-hashar.  Detta ger en prestanda förbättring under Lösenordssynkronisering från Azure AD till Azure AD Domain Services.
- Vi har lagt till stöd för tillförlitliga sessioner mellan Authentication agent och Service Bus.
- I den här versionen upprätthålls TLS 1,2 för kommunikation mellan Autentiseringstjänsten och moln tjänster.
- Vi har lagt till en DNS-cache för WebSocket-anslutningar mellan Authentication agent och moln tjänster.
- Vi har lagt till möjligheten att rikta en speciell agent från molnet för att testa anslutningen till agenten.

### <a name="fixed-issues"></a>Åtgärdade problem
- Version 1.4.18.0 hade ett fel där PowerShell-cmdleten för DSSO använde inloggnings uppgifterna för Windows i stället för de administratörsautentiseringsuppgifter som angavs när PS kördes. På grund av att det inte var möjligt att aktivera DSSO i flera skogar genom Azure AD Connect användar gränssnitt. 
- En korrigering gjordes för att aktivera DSSO samtidigt i alla skogar genom Azure AD Connect användar gränssnitt

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Versionsstatus
11/08/2019: har släppts för hämtning. Inte tillgängligt via automatisk uppgradering.

>[!IMPORTANT]
>På grund av en intern schema ändring i den här versionen av Azure AD Connect, om du hanterar konfigurations inställningar för AD FS förtroende relation med MSOnline PowerShell, måste du uppdatera MSOnline PowerShell-modulen till version 1.1.183.57 eller högre

### <a name="fixed-issues"></a>Åtgärdade problem

Den här versionen åtgärdar ett problem med befintliga hybrid Azure AD-anslutna enheter. Den här versionen innehåller en ny regel för synkronisering av enhet som åtgärdar problemet.
Observera att den här regel ändringen kan orsaka borttagning av föråldrade enheter från Azure AD. Detta är inte en anledning till problem eftersom dessa enhets objekt inte används av Azure AD under auktoriseringen för villkorlig åtkomst. För vissa kunder kan antalet enheter som tas bort via den här regel ändringen överskrida tröskelvärdet för borttagning. Om du ser borttagningen av enhets objekt i Azure AD som överskrider tröskelvärdet för borttagning av export, rekommenderas det att tillåta borttagningarna att gå igenom. [Så här tillåter du att borttagningar flödar när de överstiger tröskelvärdet för borttagning](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Versionsstatus
9/28/2019: lanseras för automatisk uppgradering för att välja klienter. Inte tillgänglig för hämtning.

Den här versionen åtgärdar ett fel där vissa servrar som automatiskt har uppgraderats från en tidigare version till 1.4.18.0 och erfarna problem med lösen ords återställning via självbetjäning (SSPR) och tillbakaskrivning av lösen ord.

### <a name="fixed-issues"></a>Åtgärdade problem

Under vissa omständigheter återaktiverade inte servrar som har uppgraderats automatiskt till 1.4.18.0 återställningen av lösen ord för självbetjäning och tillbakaskrivning av lösen ord när uppgraderingen har slutförts. Den här versionen av automatisk uppgradering åtgärdar problem som utfärdar och återaktiverar lösen ords återställning via självbetjäning och tillbakaskrivning av lösen ord.

Vi har åtgärdat ett program fel i komprimerings verktyget för synkroniseringsfel som inte hanterade surrogat tecken korrekt.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Vi undersöker en incident där vissa kunder drabbas av ett problem med befintliga hybrid Azure AD-anslutna enheter efter uppgraderingen till den här versionen av Azure AD Connect. Vi rekommenderar kunder som har distribuerat en hybrid Azure AD-anslutning för att skjuta upp uppgraderingen till den här versionen tills den bakomliggande orsaken till problemen är fullständigt förstå och minimerad. Mer information kommer att tillhandahållas så snart som möjligt.

>[!IMPORTANT]
>Med den här versionen av Azure AD Connect kan vissa kunder se vissa eller alla sina Windows-enheter försvinner från Azure AD. Detta är inte en orsak till betänkligheter eftersom dessa enhets identiteter inte används av Azure AD under auktoriseringen för villkorlig åtkomst. Mer information finns i [förstå Azure AD Connect 1.4. xx. x enhets disappearnce](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Versionsstatus
9/25/2019: endast tillgängligt för automatisk uppgradering.

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
- Nya fel söknings verktyg hjälper till med fel sökning av scenarier där "användaren inte synkroniserar", "grupp som inte synkroniserar" eller "grupp medlem inte synkroniserar".
- Lägg till stöd för nationella moln i Azure AD Connect fel söknings skript.
- Kunderna bör informeras om att de föråldrade WMI-slutpunkterna för MIIS_Service nu har tagits bort. Alla WMI-åtgärder bör nu utföras via PS-cmdletar.
- Säkerhets förbättring genom återställning av begränsad delegering för AZUREADSSOACC-objekt.
- När du lägger till/redigerar en Synkroniseringsregel, om det finns attribut som används i regeln som finns i kopplings schemat men som inte har lagts till i anslutningen, så läggs attributen automatiskt till i anslutningen. Samma sak gäller för objekt typen som regeln påverkar. Om något läggs till i anslutningen markeras kopplingen för fullständig import vid nästa synkronisering.
- Att använda en företags-eller domän administratör som kopplings konto stöds inte längre i nya Azure AD Connect-distributioner. Nuvarande Azure AD Connect distributioner som använder en företags-eller domän administratör eftersom anslutnings kontot inte påverkas av den här versionen.
- I Synkroniseringshanteraren körs en fullständig synkronisering vid skapande av regel/redigera/ta bort. En popup visas för alla regel ändringar som meddelar användaren om en fullständig import eller fullständig synkronisering kommer att köras.
- Ytterligare åtgärder för att undvika lösen ords fel har lagts till på sidan anslutningar > egenskaper > anslutning.
- En varning om utfasning har lagts till för Sync Service Manager på anslutnings egenskaper sidan. Den här varningen meddelar användaren att ändringar ska göras i guiden Azure AD Connect.
- Ett nytt fel har lagts till för problem med en användares lösen ords princip.
- Förhindra felaktig konfiguration av grupp filtrering efter domän-och OU-filter. Grupp filtrering visar ett fel när domänen/ORGANISATIONSENHETen för den angivna gruppen redan är filtrerad och hindrar användaren från att flyttas framåt tills problemet har lösts.
- Användare kan inte längre skapa en anslutning för Active Directory Domain Services eller Windows Azure Active Directory i Synchronization Service Manager gränssnittet.
- Fast tillgänglighet för anpassade GRÄNSSNITTs kontroller i Synchronization Service Manager.
- Aktiverade sex Federations hanterings uppgifter för alla inloggnings metoder i Azure AD Connect.  (Tidigare var endast uppgiften "Uppdatera AD FS TLS/SSL-certifikat" tillgänglig för alla inloggningar.)
- En varning har lagts till när du ändrade inloggnings metoden från Federation till PHS eller PTA att alla Azure AD-domäner och användare kommer att konverteras till hanterad autentisering.
- Tog bort certifikat för tokensignering från aktiviteten "Återställ Azure AD och AD FS förtroende" och lade till en separat under aktivitet för att uppdatera dessa certifikat.
- En ny Federations hanterings aktivitet med namnet "hantera certifikat", som innehåller under aktiviteter, för att uppdatera TLS-eller certifikat för tokensignering för AD FS server gruppen har lagts till.
- En ny under aktivitet för hanterings hantering lades till "Ange primär server" som gör att administratörer kan ange en ny primär server för AD FS server gruppen.
- Har lagt till en ny hanterings aktivitet med namnet "hantera servrar" som innehåller under aktiviteter för att distribuera en AD FS-server, distribuera en Webbprogramproxy och ange primär server.
- En ny Federations hanterings aktivitet som heter "Visa Federations konfiguration" har lagts till som visar de aktuella AD FS inställningarna.  (Tack vare detta läggs AD FS inställningar bort från sidan "granska din lösning".)

### <a name="fixed-issues"></a>Åtgärdade problem
- Löst problem med synkroniseringsfel för det scenario där ett användar objekt som tar över dess motsvarande kontakt objekt har en egen referens (t. ex. användare är en egen chef).
- Hjälp om popup-fönster visas nu vid tangent bords fokus.
- För automatisk uppgradering, om en motstridig App körs från 6 timmar, avsluta den och fortsätta med uppgraderingen.
- Begränsa antalet attribut som en kund kan välja till 100 per objekt när du väljer katalog tillägg. Detta förhindrar att felet uppstår under exporten eftersom Azure har högst 100 tilläggs-attribut per objekt.
- Ett fel har åtgärd ATS för att göra skriptet för AD-anslutning mer stabilt.
- En bugg har åtgärd ATS för att göra Azure AD Connect installera på en dator med hjälp av en befintlig Named Pipes WCF-tjänst mer robust.
- Förbättrad diagnostik och fel sökning kring grup principer som inte tillåter att ADSync-tjänsten startas när den startas första gången.
- Ett fel har åtgärd ATS där visnings namnet för en Windows-dator skrevs felaktigt.
- Åtgärda ett fel där OS-typen för en Windows-dator skrevs felaktigt.
- Ett fel har åtgärd ATS där icke-Windows 10-datorer synkroniserades oväntade. Observera att effekterna av den här ändringen är att icke-Windows-10-datorer som tidigare har synkroniserats nu kommer att tas bort. Detta påverkar inte några funktioner eftersom synkroniseringen av Windows-datorer endast används för Hybrid Azure AD-domän anslutning, som endast fungerar för Windows 10-enheter.
- Flera nya (interna) cmdletar har lagts till i ADSync PowerShell-modulen.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Det finns ett känt problem med att uppgradera Azure AD Connect från en tidigare version till 1.3.21.0 där Microsoft 365-portalen inte återspeglar den uppdaterade versionen, även om Azure AD Connect har uppgraderats.
>
> För att lösa detta måste du importera **ADSync** -modulen och köra `Set-ADSyncDirSyncConfiguration` PowerShell-cmdleten på Azure AD Connect-servern.  Du kan använda följande steg:
>
>1. Öppna PowerShell i administratörs läge.
>2. Kör `Import-Module "ADSync"`.
>3. Kör `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Versionsstatus 

05/14/2019: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem 

- En höjning av privilegier som finns i Microsoft Azure Active Directory Connect build-1.3.20.0 har åtgärd ATS.  Detta säkerhets problem, under vissa omständigheter, kan göra det möjligt för en angripare att köra två PowerShell-cmdletar i kontexten för ett privilegierat konto och utföra privilegierade åtgärder.  Den här säkerhets uppdateringen löser problemet genom att inaktivera dessa cmdletar. Mer information finns i [säkerhets uppdatering](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
