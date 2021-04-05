---
title: 'Azure AD Connect: logg filen för versions historik | Microsoft Docs'
description: Den här artikeln innehåller alla arkiverade utgåvor av Azure AD Connect och Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 859a4f199e65dd0c3aee9424029f6060683d5fbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836095"
---
# <a name="azure-ad-connect-version-release-history-archive"></a>Azure AD Connect: versions historik Arkiv för version

Gruppen Azure Active Directory (Azure AD) uppdaterar regelbundet Azure AD Connect med nya funktioner. Alla tillägg gäller inte för alla mål grupper.

>[!NOTE] 
> Den här artikeln innehåller information om versions referenser för alla arkiverade versioner av Azure AD-1.3.20.0 och äldre.  För aktuella versioner, se [versions historiken för Azure AD Connect](reference-connect-version-history.md)

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Versionsstatus 

04/24/2019: har släppts för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 

- Lägg till stöd för domän uppdatering 
- Funktionen funktioner för offentliga Exchange-mappar går GA 
- Förbättra fel hanteringen i guiden för tjänst fel 
- Varnings länken har lagts till i Synchronization Service Manager UI på sidan anslutnings egenskaper. 
- Funktionen tillbakaskrivning av enhetliga grupper är nu GA 
- Förbättrat SSPR-felmeddelande när DOMÄNKONTROLLANTen saknar en LDAP-kontroll 
- Diagnostik för DCOM-register fel har lagts till under installationen  
- Förbättrad spårning av PHS RPC-fel 
- Tillåt EA-autentiseringsuppgifter från en underordnad domän 
- Tillåt att databas namnet anges under installationen (standard namn ADSync)
- Uppgradera till ADAL 3.19.8 för att hämta en WS-Trust korrigering för ping och lägga till stöd för nya Azure-instanser 
- Ändra regler för grupp synkronisering till Flow samAccountName, DomainNetbios och DomainFQDN till molnet – behövs för anspråk 
- Ändrad standard hantering av Synkroniseringsregel – Läs mer [här](how-to-connect-fix-default-rules.md).
- En ny agent som körs som en Windows-tjänst har lagts till. Den här agenten med namnet "admin agent" möjliggör djupare fjärrdiagnostik av Azure AD Connect-servern så att Microsoft-tekniker kan felsöka när du öppnar ett support ärende. Den här agenten är inte installerad och aktive rad som standard.  Mer information om hur du installerar och aktiverar agenten finns i [Vad är Azure AD Connect admin agent?](whatis-aadc-admin-agent.md). 
- Licens avtalet för slutanvändare (EULA) har uppdaterats 
- Stöd för automatisk uppgradering har lagts till för distributioner som använder AD FS som inloggnings typ.  Detta tog också bort kravet på att uppdatera AD FS förlitande parts förtroende för Azure AD som en del av uppgraderings processen. 
- Lade till en Azure AD Trust Management-uppgift som innehåller två alternativ: analysera/uppdatera förtroende och Återställ förtroende. 
- Ändrade det förlitande part förtroendet för AD FS Azure AD, så att det alltid använder växeln-SupportMultipleDomain (innehåller förtroende och Azure AD-domän uppdateringar). 
- Ändrade beteendet installera ny AD FS server grupp så att det kräver ett. PFX-certifikat genom att ta bort alternativet att använda ett förinstallerat certifikat.
- Uppdaterade arbets flödet installera ny AD FS server grupp så att det bara tillåter distribution av 1 AD FS och 1 WAP-server.  Alla ytterligare servrar kommer att utföras efter den första installationen. 

### <a name="fixed-issues"></a>Åtgärdade problem 

- Korrigera SQL reconnect-logiken för ADSync-tjänsten 
- Korrigera för att tillåta ren installation med en tom SQL AOA DB 
- Reparera PS Permissions-skript för att förfina GWB-behörigheter 
- Åtgärda VSS-fel med LocalDB  
- Åtgärda missvisande fel meddelande när objekt typen inte är inom omfånget 
- Ett problem har åtgärd ATS där installationen av Azure AD PowerShell på en server kan orsaka en sammansättnings konflikt med Azure AD Connect. 
- Fast PHS-bugg på mellanlagringsplatsen när autentiseringsuppgifter för anslutning har uppdaterats i Synchronization Service Manager användar gränssnitt. 
- Fasta minnes läckor 
- Diverse korrigeringar för autouppgradering 
- Diverse korrigeringar för export och obekräftad import bearbetning 
- En bugg har åtgärd ATS vid hantering av omvänt snedstreck i domän-och OU-filtrering 
- Ett problem har åtgärd ATS där ADSync-tjänsten tar över 2 minuter att stoppas och orsakar problem vid uppgraderings tiden. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Versionsstatus

12/18/2018: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem

Den här versionen uppdaterar icke-standardanslutningarna (till exempel allmän LDAP-anslutning och allmän SQL-anslutning) som levererades med Azure AD Connect. Mer information om tillämpliga anslutningar finns i version 1.1.911.0 i versions [historik för Connector](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Versionsstatus
12/11/2018: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
Med den här snabb korrigeringen kan användaren välja en mål domän inom den angivna skogen för behållaren RegisteredDevices när du aktiverar tillbakaskrivning av enheten.  I tidigare versioner som innehåller de nya funktionerna för enhets alternativ (1.1.819.0 – 1.2.68.0) var RegisteredDevices container-platsen begränsad till skogs roten och tillät inte underordnade domäner.  Den här begränsningen visas bara för nya distributioner – uppgraderingar på plats påverkas inte.  

Om en version som innehåller de uppdaterade enhets alternativen har distribuerats till en ny server och tillbakaskrivning av enheten har Aktiver ATS, måste du ange platsen för behållaren manuellt om du inte vill att den ska vara med i skogs roten.  Om du vill göra det måste du inaktivera tillbakaskrivning av enheten och aktivera den igen, så att du kan ange behållar platsen på sidan "tillbakaskrivning skog".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Versionsstatus 

11/30/2018: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem

Den här snabb korrigeringen åtgärdar en konflikt där ett autentiseringsfel kan uppstå på grund av den oberoende förekomsten av MSOnline-PowerShell-galleriet-modulen på synkroniseringstjänsten.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Versionsstatus 

11/19/2018: har släppts för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem

Den här snabb korrigeringen åtgärdar en regression i den tidigare versionen där tillbakaskrivning av lösen ord Miss lyckas när en lägger till en domänkontrollant i Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Versionsstatus 

10/25/2018: har släppts för nedladdning


### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 


- Ändrade funktionen för tillbakaskrivning av attribut för att säkerställa att Hosted Voice Mail fungerar som förväntat.  Under vissa scenarier skrevs Azure AD över msExchUcVoicemailSettings-attributet vid Skriv-tillbaka med ett null-värde.  Azure AD tar nu inte längre bort det lokala värdet för det här attributet om molnets värde inte har angetts.
- Diagnostik har lagts till i guiden Azure AD Connect för att undersöka och identifiera anslutnings problem till Azure AD. Samma diagnostik kan också köras direkt via PowerShell med cmdleten test-AdSyncAzureServiceConnectivity. 
- Diagnostik har lagts till i guiden Azure AD Connect för att undersöka och identifiera anslutnings problem till AD. Samma diagnostik kan också köras direkt via PowerShell med hjälp av funktionen Start-ConnectivityValidation i PowerShell-modulen ADConnectivityTools.  Mer information finns i [Vad är ADConnectivityTool PowerShell-modulen?](how-to-connect-adconnectivitytools.md)
- Har lagt till en AD-schema version för för hands kontroll av hybrid Azure Active Directory anslutning och enhets skrivning 
- Ändring av sökattributet för katalog tillägg är inte Skift läges känsliga.
-   Fullständigt stöd för TLS 1,2 har lagts till. Den här versionen stöder alla andra protokoll som inaktive ras och endast TLS 1,2 aktive ras på den dator där Azure AD Connect är installerat.  Mer information finns i [tillämpning av TLS 1,2 för Azure AD Connect](reference-connect-tls-enforcement.md)



### <a name="fixed-issues"></a>Åtgärdade problem   

- En bugg har åtgärd ATS där Azure AD Connect uppgraderingen skulle bli misslyckad om SQL Always on användes. 
- En bugg har åtgärd ATS för att korrekt tolka ORGANISATIONSENHETs namn som innehåller ett snedstreck. 
- Ett problem har åtgärd ATS där Pass-Through autentisering skulle inaktive ras för en ren installation i mellanlagrings läge. 
- En bugg har åtgärd ATS som gjorde att PowerShell-modulen lästes in när fel söknings verktyg kördes 
- En bugg har åtgärd ATS som hindrar kunder från att använda numeriska värden i det första tecken i ett värdnamn. 
- Ett fel där Azure AD Connect skulle tillåta ogiltiga partitioner och val av behållare har åtgärd ATS 
- Fel meddelandet "ogiltigt lösen ord" har åtgärd ATS när enkel inloggning på Skriv bordet är aktiverat. 
- Olika fel korrigeringar för AD FS Trust Management  
- När du konfigurerar en tillbakaskrivning av enheten – korrigerar du schema kontrollen för att leta efter objekt klassen msDs-DeviceContainer (lanserad på WS2012 R2)


## <a name="118820"></a>1.1.882.0  

9/7/2018: har släppts för nedladdning, kommer inte att lanseras för automatisk uppgradering 

### <a name="fixed-issues"></a>Åtgärdade problem  

Azure AD Connect uppgraderingen Miss lyckas om SQL Always on-tillgänglighet har kon figurer ATS för ADSync-databasen. Den här snabb korrigeringen löser problemet och gör att uppgraderingen lyckas. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Versionsstatus

8/21/2018: lanseras för hämtning och automatisk uppgradering. 

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- Ping Federer-integrationen i Azure AD Connect är nu tillgänglig för allmän tillgänglighet. [Läs mer om hur du federerade Azure AD med ping Federer](./plan-connect-user-signin.md#federation-with-pingfederate)
- Azure AD Connect skapar nu säkerhets kopian av Azure AD-förtroende i AD FS varje gång en uppdatering görs och lagras i en separat fil för enkel återställning om det behövs. [Läs mer om de nya funktionerna och Azure AD Trust Management i Azure AD Connect](./how-to-connect-azure-ad-trust.md).
- Nya fel söknings verktyg hjälper dig att felsöka ändring av primär e-postadress och dölja konto från Global adress lista
- Azure AD Connect uppdaterades till att omfatta den senaste SQL Server 2012-interna klienten
- När du växlar användar inloggning till hash-synkronisering av lösen ord eller direktautentisering i uppgiften "ändra användarens inloggning", aktive ras kryss rutan sömlöst Sign-On som standard.
- Stöd har lagts till för Windows Server Essentials 2019
- Azure AD Connect Health-agenten har uppdaterats till den senaste versionen av 3.1.7.0
- Om installations programmet identifierar ändringar i standard reglerna för synkronisering under en uppgradering, uppmanas administratören att ange en varning innan de ändrade reglerna skrivs över. Detta gör att användaren kan vidta korrigerande åtgärder och återuppta senare. Gammalt beteende: om det fanns någon ändrad regel för direkt inaktive ring skrevs den manuella uppgraderingen över dessa regler utan att ge någon varning till användaren och synkronisering av Schemaläggaren inaktiverades utan att användaren informeras. Nytt beteende: användaren uppmanas att varna innan de ändrade reglerna för synkronisering av den ändrade gruppen skrivs över. Användaren kan välja att stoppa uppgraderings processen och återuppta senare efter att ha vidtagit åtgärd.
- Ge en bättre hantering av ett problem med FIPS-kompatibilitet, vilket ger ett fel meddelande för generering av MD5-hash i en FIPS-kompatibel miljö och en länk till dokumentation som ger ett sådant problem.
- UI-uppdatering för att förbättra Federations aktiviteterna i guiden, som nu finns under en separat under grupp för federationen. 
- Alla ytterligare uppgifter för federationen grupperas nu under en enda underordnad meny för enkel användning.
- En ny förbättringar ADSyncConfig posh module (AdSyncConfig. psm1) med nya funktioner för AD-behörighet flyttas från den gamla ADSyncPrep. psm1 (som kan vara inaktuell inom kort)

### <a name="fixed-issues"></a>Åtgärdade problem 

- En bugg har åtgärd ATS där Azure AD Connect servern skulle visa hög CPU-användning efter uppgradering till .NET 4.7.2
- En bugg har åtgärd ATS som skapar ett fel meddelande tillfälligt för ett automatiskt löst problem med SQL-deadlock
- Åtgärdat flera tillgänglighets problem för redigeraren för synkronisering av regler och synkronisering Service Manager  
- En bugg har åtgärd ATS där Azure AD Connect inte kan hämta information om register inställningar
- En bugg har åtgärd ATS som skapade problem när användaren går framåt/bakåt i guiden
- En bugg har åtgärd ATS för att förhindra att ett fel inträffar på grund av felaktig multi-Thread-hand i guiden
- Om sidan för synkronisering av synkronisering påträffar ett LDAP-fel vid matchning av säkerhets grupper, kan Azure AD Connect nu returnera undantaget med fullständig åter givning.  Rotor saken för hänvisnings undantaget är fortfarande okänt och kommer att åtgärdas av en annan bugg.
-  Ett fel har åtgärd ATS där behörigheter för STK-och NGC-nycklar (ms-DS-KeyCredentialLink-attribut för användar-/enhets objekt för WHfB) inte har angetts korrekt.     
- Ett fel har åtgärd ATS där "Set-ADSyncRestrictedPermissions" inte anropades korrekt
-  Lägga till stöd för behörighets beviljande för tillbakaskrivning av grupp i Azure ADConnects installations guide
- När du ändrar inloggnings metod från hash-synkronisering av lösen ord till AD FS inaktiverades inte lösen ordets hash-synkronisering.
- Kontroll av IPv6-adresser i AD FS konfiguration har lagts till
- Meddelandet har uppdaterats för att informera om att det finns en befintlig konfiguration.
- Tillbakaskrivning av enhet kan inte identifiera behållare i en skog som inte är betrodd. Detta har uppdaterats för att ge ett bättre fel meddelande och en länk till lämplig dokumentation
- Om du avmarkerar en ORGANISATIONSENHET och sedan synkronisering/tillbakaskrivning som motsvarar den ORGANISATIONSENHETen får du ett allmänt synkroniseringsfel. Detta har ändrats för att skapa ett mer begripligt fel meddelande.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Versionsstatus

5/14/2018: lanseras för automatisk uppgradering och hämtning.

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

Nya funktioner och förbättringar

- Den här versionen innehåller den offentliga för hands versionen av integreringen av PingFederate i Azure AD Connect. Med den här versionen kan kunder enkelt och på ett tillförlitligt sätt konfigurera sin Azure Active Directorys miljö för att utnyttja PingFederate som Federations leverantör. Om du vill veta mer om hur du använder den här nya funktionen kan du [besöka vår onlinedokumentation](plan-connect-user-signin.md#federation-with-pingfederate). 
- Uppdaterade fel söknings verktyget för Azure AD Connect, där det nu analyserar fler fel scenarier, till exempel länkade post lådor och dynamiska AD-grupper. Läs mer om fel söknings verktyget [här](tshoot-connect-objectsync.md).
- Konfigurationen för tillbakaskrivning av enhet hanteras nu bara i Azure AD Connect guiden.
- En ny PowerShell-modul med namnet ADSyncTools. psm1 har lagts till som kan användas för att felsöka problem med SQL-anslutningen och olika fel söknings verktyg. Läs mer om ADSyncTools-modulen [här](tshoot-connect-tshoot-sql-connectivity.md). 
- En ny ytterligare uppgift "Konfigurera enhets alternativ" har lagts till. Du kan använda aktiviteten för att konfigurera följande två åtgärder: 
  - **Hybrid Azure AD-anslutning**: om din miljö har ett lokalt AD-utrymme och du även vill ha nytta av de funktioner som tillhandahålls av Azure Active Directory kan du implementera hybrid Azure AD-anslutna enheter. Det är enheter som är anslutna både till din lokala Active Directory och din Azure Active Directory.
  - **Tillbakaskrivning av enhet**: enhetens tillbakaskrivning används för att aktivera villkorlig åtkomst baserat på enheter till AD FS (2012 R2 eller högre) skyddade enheter

    >[!NOTE] 
    > - Alternativet att aktivera tillbakaskrivning av enheter från Anpassa synkroniseringsalternativ blir grå. 
    > -  PowerShell-modulen för ADPrep är föråldrad i den här versionen.


### <a name="fixed-issues"></a>Åtgärdade problem 

- Den här versionen uppdaterar SQL Server Express-installationen till SQL Server 2012 SP4, som bland annat innehåller korrigeringar för flera säkerhets problem.  Mer information om SQL Server 2012 SP4 finns [här](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) .
- Synkronisera regel bearbetning: regler för utgående anslutning utan kopplings villkor ska tas bort om den överordnade synkroniseringsregeln inte längre är tillämplig
- Flera tillgänglighets korrigeringar har tillämpats på Synchronization Service Manager gränssnittet och redigeraren för regler för synkronisering
- Guiden Azure AD Connect: det gick inte att skapa ett AD Connector-konto när Azure AD Connect finns i en arbets grupp
- Guiden Azure AD Connect: på inloggnings sidan för Azure AD visas kryss rutan Verifiera när det finns matchnings fel i AD-domäner och verifierade Azure AD-domäner
- Automatisk uppgradering av PowerShell-korrigering för att ange statusen för automatisk uppgradering korrekt i vissa fall efter det att den automatiska uppgraderingen har gjorts.
- Azure AD Connect guide: uppdaterad telemetri för att samla in tidigare saknad information
- Azure AD Connect guiden: följande ändringar har gjorts när du använder **inloggnings uppgiften ändra användare** för att växla från AD FS till vidarekoppling:
    - Genom strömnings agenten installeras på Azure AD Connect-servern och funktionen för direkt autentisering är aktive rad innan vi konverterar domän (er) från federerade till hanterade.
    - Användarna konverteras inte längre från federerade till hanterade. Endast domän (er) har konverterats.
- Guiden Azure AD Connect: AD FS flera domän-regex är inte rätt när UPN för användare har en speciell teckens regex-uppdatering som stöd för specialtecken
- Azure AD Connect guide: ta bort meddelandet "Konfigurera källans ankare" när ingen ändring har ändrats 
- Guiden Azure AD Connect: AD FS stöd för scenariot med dubbel Federation
- Guiden Azure AD Connect: AD FS-anspråk uppdateras inte för tillagda domäner när en hanterad domän konverteras till federerad
- Guiden Azure AD Connect: under identifieringen av installerade paket hittar vi föråldrade DirSync/Azure AD Sync/Azure AD Connect relaterade produkter. Vi försöker nu avinstallera de inaktuella produkterna.
- Guiden Azure AD Connect: korrigera fel meddelande mappning när installationen av PassThrough-autentisering Miss lyckas
- Guiden Azure AD Connect: tog bort behållaren "konfiguration" från sidan för domän organisations filtrering
- Synkronisering av motor: ta bort onödig tidigare logik som ibland Miss lyckas från Synkroniseringsmotorn installera MSI
- Guiden Azure AD Connect: korrigera popup-hjälp text på valfria funktioner-sidan för synkronisering av lösen ord för hash
- Motorn för synkronisering av synkronisering: korrigera scenariot där ett CS-objekt har importerade regler för borttagning och synkronisering försök att etablera om objektet.
- Synkronisering motor körning: Lägg till hjälp länk för fel söknings guide för online-anslutning till händelse loggen för ett import fel
- Synkronisering av motor körning: minskad minnes användning för synkronisering Schemaläggaren vid uppräkning av anslutningar
- Guiden Azure AD Connect: åtgärda ett problem med att lösa ett konto för en anpassad synkroniseringstjänst som inte har Läs behörighet för AD
- Azure AD Connect guide: förbättra loggning av val av domän-och OU-filtrering
- Guiden Azure AD Connect: AD FS Lägg till standard anspråk till Federations förtroende som skapats för MFA-scenario
- Guiden Azure AD Connect: AD FS distribuera WAP: det går inte att använda det nya certifikatet för att lägga till servern
- Azure AD Connect guide: DSSO-undantag när onPremCredentials inte har initierats för en domän 
- Flödar AD distinguishedName-attributet på ett prioriterat från objektet Active User.
- Fast en kosmetisk bugg var prioriteten för den första OOB-synkroniseringsregeln har angetts till 99 i stället för 100



## <a name="117510"></a>1.1.751.0
Status 4/12/2018: endast tillgängligt för hämtning

>[!NOTE]
>Den här versionen är en snabb korrigering för Azure AD Connect
### <a name="azure-ad-connect-sync"></a>Azure AD Connect-synkronisering
#### <a name="fixed-issues"></a>Åtgärdade problem
Ett problem har åtgärd ATS automatiskt när Azure-instansen skulle identifieras för Kina-klienter.  

### <a name="ad-fs-management"></a>AD FS hantering
#### <a name="fixed-issues"></a>Åtgärdade problem

Det uppstod ett problem i den logiska konfigurationens återförsöks logik som skulle resultera i ett ArgumentException som anger att ett objekt med samma nyckel redan har lagts till.  Detta gör att alla återförsök inte kan utföras.

## <a name="117500"></a>1.1.750.0
Status 3/22/2018: lanseras för automatisk uppgradering och hämtning.
>[!NOTE]
>När uppgraderingen till den här nya versionen har slutförts, utlöses automatiskt en fullständig synkronisering och fullständig import för Azure AD-anslutningen och en fullständig synkronisering för AD-anslutningen. Eftersom detta kan ta lite tid, beroende på storleken på din Azure AD Connect miljö, se till att du har vidtagit de åtgärder som krävs för att stöda det eller hålla på att uppgradera tills du har hittat en lämplig tidpunkt.
>[!NOTE]
>"Funktionen för autouppgradering har inaktiverats felaktigt för vissa klienter som distribuerade versioner senare än 1.1.524.0. Kör följande PowerShell-cmdlet för att kontrol lera att Azure AD Connect-instansen fortfarande är tillgänglig för autouppgradering: "Set-ADSyncAutoUpgrade-AutoupGradeState Enabled"

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem

* Set-ADSyncAutoUpgrade cmdlet skulle tidigare blockera automatisk uppgradering om status för automatisk uppgradering är inställd på pausad. Den här funktionen har nu ändrats så att den inte blockerar autouppgradering av framtida versioner.
* Ändrade alternativet för **inloggnings** sidan för användaren "Lösenordssynkronisering" till "Password hash Sync".  Azure AD Connect synkroniserar lösen ordets hash-värden, inte lösen ord, så detta justeras med vad som faktiskt inträffar.  Mer information finns i [implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Status: släpps för utvalda kunder

>[!NOTE]
>När uppgraderingen till den här nya versionen har slutförts, utlöses automatiskt en fullständig synkronisering och fullständig import för Azure AD-anslutningen och en fullständig synkronisering för AD-anslutningen. Eftersom detta kan ta lite tid, beroende på storleken på din Azure AD Connect miljö, se till att du har vidtagit de åtgärder som krävs för att stödja eller hålla på att uppgradera tills du har hittat en lämplig tidpunkt.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärda tids periods fönstret för bakgrunds aktiviteter för partitions filtrerings sidan när du växlar till nästa sida.

* En bugg har åtgärd ATS som orsakade åtkomst fel under den anpassade ConfigDB-åtgärden.

* En bugg har åtgärd ATS för att återställa från SQL-anslutningstimeout.

* Ett fel har åtgärd ATS där certifikat med SAN-jokertecken misslyckades med en krav kontroll.

* En bugg har åtgärd ATS som gör att miiserver.exe kraschar under en Azure AD Connector-export.

* Ett fel har åtgärd ATS som felaktig lösen ords försök har loggat in på DOMÄNKONTROLLANTen när guiden Azure AD Connect kördes för att ändra konfigurationen.


#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Lägga till sekretess inställningar för allmän dataskyddsförordning (GDPR).  Mer information finns i artikeln [här](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* programtelemetri – administratören kan ändra den här data klassen på/av

* Azure AD Health data – administratören måste gå till hälso portalen för att kontrol lera sina hälso inställningar.
   När tjänst principen har ändrats, kommer agenterna att läsa och tillämpa den.

* Tillagda konfigurations åtgärder för enhets skrivning och en förlopps indikator för sid initiering

* Förbättrad allmän diagnostik med HTML-rapport och fullständig data insamling i en ZIP-Text/HTML-rapport

* Förbättrad tillförlitlighet för automatisk uppgradering och ytterligare telemetri för att säkerställa att serverns hälso tillstånd kan fastställas

* Begränsa behörigheter som är tillgängliga för privilegierade konton i AD Connector-konto

  * För nya installationer begränsar guiden de behörigheter som privilegierade konton har på MSOL-kontot när MSOL-kontot har skapats.

Ändringarna tar hand om följande:
1. Express installationer
2. Anpassade installationer med konto för automatisk skapande
3. Ändrade installations programmet så att det inte kräver SA-behörighet vid ren installation av Azure AD Connect

* Ett nytt verktyg har lagts till för att Felsöka synkroniseringsproblem för ett bestämt objekt. Den är tillgänglig under alternativet "Felsök objekt synkronisering" i Azure AD Connects guide fel sökning av ytterligare uppgift. För närvarande söker verktyget efter följande:

  * UserPrincipalName matchnings fel mellan synkroniserat användar objekt och användar kontot i Azure AD-klienten.
  * Om objektet filtreras från synkroniseringen på grund av domän filtrering
  * Om objektet filtreras från synkroniseringen på grund av organisations enhetens (OU)-filtrering

* Ett nytt verktyg har lagts till för att synkronisera den aktuella lösen ords-hashen som lagras i den lokala Active Directory för ett särskilt användar konto.

Verktyget kräver ingen lösen ords ändring. Alternativet är tillgängligt under "Felsöka lösen ords-hash-synkronisering" i Azure AD Connects guide fel sökning av ytterligare uppgift.






## <a name="116540"></a>1.1.654.0
Status: 12 december, 2017

>[!NOTE]
>Den här versionen är en säkerhetsrelaterad snabb korrigering för Azure AD Connect
### <a name="azure-ad-connect"></a>Azure AD Connect
En förbättring har lagts till Azure AD Connect version 1.1.654.0 (och efter) för att se till att de rekommenderade behörighets ändringarna som beskrivs i avsnittet [låsa ned åtkomst till AD DS-kontot](#lock) automatiskt tillämpas när Azure AD Connect skapar AD DS-kontot. 

- När du konfigurerar Azure AD Connect kan installations administratören antingen ange ett befintligt AD DS-konto eller låta Azure AD Connect skapa kontot automatiskt. Behörighets ändringarna tillämpas automatiskt på det AD DS-konto som skapas i Azure AD Connect under installationen. De tillämpas inte på det befintliga AD DS-konto som tillhandahålls av den installerande administratören.
- För kunder som har uppgraderat från en äldre version av Azure AD Connect till 1.1.654.0 (eller senare) tillämpas inte behörighets ändringarna retroaktivt på befintliga AD DS-konton som skapats före uppgraderingen. De kommer endast att tillämpas på nya AD DS-konton som skapats efter uppgraderingen. Detta inträffar när du lägger till nya AD-skogar som ska synkroniseras till Azure AD.

>[!NOTE]
>Den här versionen tar bara bort säkerhets problemet för nya installationer av Azure AD Connect där tjänst kontot skapas vid installationen. För befintliga installationer, eller i de fall där du anger kontot själv, bör du se till att säkerhets problemet inte finns.
#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> Lås åtkomst till AD DS-kontot
Lås åtkomst till AD DS-kontot genom att implementera följande behörighets ändringar i den lokala AD-miljön:  

*   Inaktivera arv för det angivna objektet
*   Ta bort alla ACE: er för det angivna objektet, förutom åtkomst till sig själv. Vi vill behålla standard behörigheterna när det kommer till dig själv.
*   Tilldela följande behörigheter:

Typ     | Name                          | Access               | Gäller för
---------|-------------------------------|----------------------|--------------|
Tillåt    | SYSTEM                        | Fullständig kontroll         | Det här objektet  |
Tillåt    | Företagsadministratörer             | Fullständig kontroll         | Det här objektet  |
Tillåt    | Domänadministratörer                 | Fullständig kontroll         | Det här objektet  |
Tillåt    | Administratörer                | Fullständig kontroll         | Det här objektet  |
Tillåt    | Företagets domänkontrollanter | Lista innehåll        | Det här objektet  |
Tillåt    | Företagets domänkontrollanter | Läs alla egenskaper  | Det här objektet  |
Tillåt    | Företagets domänkontrollanter | Läs behörigheter     | Det här objektet  |
Tillåt    | Autentiserade användare           | Lista innehåll        | Det här objektet  |
Tillåt    | Autentiserade användare           | Läs alla egenskaper  | Det här objektet  |
Tillåt    | Autentiserade användare           | Läs behörigheter     | Det här objektet  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-skript för att dra av ett redan befintligt tjänst konto

Om du vill använda PowerShell-skriptet för att tillämpa de här inställningarna på ett befintligt AD DS-konto, som skapats av din organisation eller skapats av en tidigare installation av Azure AD Connect, kan du ladda ned skriptet från den angivna länken ovan.

##### <a name="usage"></a>Användning:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Var 

**$ObjectDN** = det Active Directory konto vars behörigheter måste höjas.

**$Credential** = administratörs behörighet som har de behörigheter som krävs för att begränsa behörigheterna för $ObjectDN-kontot. De här behörigheterna brukar innehas av företags-eller domän administratören. Använd det fullständigt kvalificerade domän namnet för administratörs kontot för att undvika konto uppslags problem. Exempel: contoso. com\admin.

>[!NOTE] 
>$credential. Användar namnet ska vara i FQDN\username-format. Exempel: contoso. com\admin 
##### <a name="example"></a>Exempel:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Har det här säkerhets problemet använts för att få obehörig åtkomst?

För att se om säkerhets problemet har använts för att kompromettera din Azure AD Connect-konfiguration bör du kontrol lera det senaste datumet för återställning av lösen ord för tjänst kontot.  Om tidsstämpeln i oväntad händelse logg, via händelse loggen, för den här lösen ords återställningen ska ske.

Mer information finns i [Microsoft Security Advisory 4056318](/security-updates/securityadvisories/2017/4056318)

## <a name="116490"></a>1.1.649.0
Status: 27 2017 oktober

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder med hjälp av funktionen för automatisk uppgradering av Azure AD Connect.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Åtgärdat problem 
* Åtgärdat kompatibilitetsproblem mellan Azure AD Connect och Azure AD Connect Health Agent (för synkronisering). Det här problemet påverkar kunder som utför Azure AD Connect uppgradering på plats till version 1.1.647.0, men som för närvarande har 3.0.127.0 för hälso tillstånds agent. Efter uppgraderingen kan hälso agenten inte längre skicka hälso information om Azure AD Connect-synkroniseringstjänsten till Azure AD Hälsotjänst. Med den här korrigeringen installeras Health Agent-version 3.0.129.0 under Azure AD Connect uppgradering på plats. 3.0.129.0 för hälso tillstånds agenten har inte kompatibilitetsproblem med Azure AD Connect version 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Status: 19 2017 oktober

> [!IMPORTANT]
> Det finns ett känt kompatibilitetsproblem mellan Azure AD Connect version 1.1.647.0 och Azure AD Connect Health Agent (för synkronisering) version 3.0.127.0. Det här problemet förhindrar att hälso agenten skickar hälso data om Azure AD Connect synkroniseringstjänst (inklusive synkroniseringsfel och köra historik data) till Azure AD Hälsotjänst. Innan du uppgraderar din Azure AD Connect-distribution till version 1.1.647.0 måste du verifiera den aktuella versionen av Azure AD Connect Health-agenten som är installerad på Azure AD Connect-servern. Du kan göra det genom att gå till *kontroll panelen → Lägg till ta bort program* och leta efter program *Microsoft Azure AD ansluta Health Agent för synkronisering*. Om versionen är 3.0.127.0 rekommenderar vi att du väntar på nästa Azure AD Connect version som ska vara tillgänglig innan du uppgraderar. Om hälso agent versionen inte är 3.0.127.0 är det bra att fortsätta med den manuella uppgraderingen på plats. Observera att det här problemet inte påverkar flyttnings uppgradering eller kunder som utför en ny installation av Azure AD Connect.
>
>
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem
* Ett problem har åtgärd ATS med *inloggnings uppgiften ändra användare* i Azure AD Connect guiden:

  * Problemet uppstår när du har en befintlig Azure AD Connect distribution med Lösenordssynkronisering **aktiverat** och du försöker ange användar inloggnings metoden som *direktautentisering*. Innan ändringen verkställs visas meddelandet "*inaktivera lösenordssynkronisering*" i guiden. Lösenordssynkronisering förblir dock aktiverat när ändringen har tillämpats. Med den här korrigeringen visar guiden inte längre prompten.

  * Som design inaktiverar guiden inte Lösenordssynkronisering när du uppdaterar användar inloggnings metoden med hjälp av uppgiften *ändra användarens inloggning* . Detta är för att undvika avbrott i kunder som vill behålla Lösenordssynkronisering, även om de aktiverar direktautentisering eller Federation som sin primära användar inloggnings metod.

  * Om du vill inaktivera lösenordssynkronisering när du har uppdaterat användar inloggnings metoden måste du köra åtgärden *Anpassa konfiguration av synkronisering* i guiden. När du går till sidan *valfria funktioner* avmarkerar du alternativet *Lösenordssynkronisering* .

  * Observera att samma problem även uppstår om du försöker aktivera/inaktivera sömlös enkel inloggning. Mer specifikt har du en befintlig Azure AD Connect distribution med Lösenordssynkronisering aktiverat och användar inloggnings metoden redan kon figurer ATS som *direktautentisering*. Med hjälp av uppgiften *ändra användarens inloggning* försöker du markera/avmarkera alternativet *Aktivera sömlös enkel inloggning* medan användar inloggnings metoden fortfarande är konfigurerad som "direktautentisering". Innan ändringen verkställs visas meddelandet "*inaktivera lösenordssynkronisering*" i guiden. Lösenordssynkronisering förblir dock aktiverat när ändringen har tillämpats. Med den här korrigeringen visar guiden inte längre prompten.

* Ett problem har åtgärd ATS med *inloggnings uppgiften ändra användare* i Azure AD Connect guiden:

  * Problemet uppstår när du har en befintlig Azure AD Connect distribution med Lösenordssynkronisering **inaktiverat** och du försöker ange användar inloggnings metoden som *direktautentisering*. När ändringen tillämpas aktiverar guiden både direktautentisering och Lösenordssynkronisering. Med den här korrigeringen kan guiden inte längre aktivera Lösenordssynkronisering.

  * Tidigare var Lösenordssynkronisering ett krav för att aktivera direktautentisering. När du ställer in användar inloggnings metoden som *direktautentisering*, aktiverar guiden både genom strömnings autentisering och Lösenordssynkronisering. Nyligen har Lösenordssynkronisering tagits bort som ett krav. Som en del av Azure AD Connect version 1.1.557.0 gjordes en ändring av Azure AD Connect att inte aktivera Lösenordssynkronisering när du angav användar inloggnings metoden som *direktautentisering*. Ändringen tillämpades dock endast för Azure AD Connect installationen. Med den här korrigeringen används även samma ändring för *inloggnings uppgiften change user* .

  * Observera att samma problem även uppstår om du försöker aktivera/inaktivera sömlös enkel inloggning. Mer specifikt har du en befintlig Azure AD Connect distribution med Lösenordssynkronisering inaktiverat och användar inloggnings metoden redan har kon figurer ATS som *direktautentisering*. Med hjälp av uppgiften *ändra användarens inloggning* försöker du markera/avmarkera alternativet *Aktivera sömlös enkel inloggning* medan användar inloggnings metoden fortfarande är konfigurerad som "direktautentisering". När ändringen har tillämpats aktiverar guiden Lösenordssynkronisering. Med den här korrigeringen kan guiden inte längre aktivera Lösenordssynkronisering. 

* Ett problem som orsakade Azure AD Connect uppgraderingen kunde inte slutföras, fel meddelandet "*Det gick inte att uppgradera synkroniseringstjänsten*". Dessutom kan synkroniseringstjänsten inte längre starta med ett händelse fel:*Det gick inte att starta tjänsten eftersom databasens version är nyare än den installerade binärfilens version*. Problemet uppstår när administratören som utför uppgraderingen inte har sysadmin-behörighet till den SQL Server som används av Azure AD Connect. Med den här korrigeringen kräver Azure AD Connect bara att administratören ska ha db_owner behörighet till ADSync-databasen under uppgraderingen.

* Åtgärdat ett Azure AD Connect uppgraderings problem som påverkade kunder som har aktiverat [sömlös enkel inloggning](./how-to-connect-sso.md). När Azure AD Connect har uppgraderats visas sömlöst Sign-On felaktigt som inaktiverat i Azure AD Connect-guiden, även om funktionen fortfarande är aktive rad och fungerar som den ska. Med den här korrigeringen visas nu funktionen korrekt som aktive rad i guiden.

* Åtgärdat ett problem som orsakade Azure AD Connects guiden att alltid Visa prompten "*Konfigurera käll ankare*" på sidan *klar att konfigurera* , även om inga ändringar som rör käll ankare har gjorts.

* När du utför en manuell uppgradering av Azure AD Connect måste kunden ange den globala administratörs behörigheten för motsvarande Azure AD-klient. Tidigare kunde uppgraderingen fortsätta även om den globala administratörens autentiseringsuppgifter tillhör en annan Azure AD-klient. När uppgraderingen har slutförts är vissa konfigurationer inte korrekt sparade med uppgraderingen. Med den här ändringen förhindrar guiden att uppgraderingen fortsätter om de angivna autentiseringsuppgifterna inte matchar Azure AD-klienten.

* Redundant logik togs bort som inte nödvändigt vis startats om Azure AD Connect Health tjänsten i början av en manuell uppgradering.


#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Har lagt till logik för att förenkla de steg som krävs för att konfigurera Azure AD Connect med Microsoft Germany-molnet. Tidigare var du tvungen att uppdatera vissa register nycklar på Azure AD Connect-servern för att den ska fungera korrekt med Microsoft Germany-molnet, enligt beskrivningen i den här artikeln. Nu kan Azure AD Connect automatiskt identifiera om din klient organisation finns i Microsoft Tyskland-moln baserat på de globala administratörs autentiseringsuppgifterna som angavs under installationen.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Obs! synkroniseringstjänsten har ett WMI-gränssnitt som gör att du kan utveckla din egen anpassade schemaläggare. Det här gränssnittet är nu föråldrat och kommer att tas bort från framtida versioner av Azure AD Connect som levererats efter den 30 juni 2018. Kunder som vill anpassa synkroniseringsschemat bör använda den [inbyggda Schemaläggaren](./how-to-connect-sync-feature-scheduler.md).
#### <a name="fixed-issues"></a>Åtgärdade problem
* När Azure AD Connects guide skapar det AD Connector-konto som krävs för att synkronisera ändringar från den lokala Active Directory, tilldelar det inte rätt konto behörighet som krävs för att läsa PublicFolder-objekt. Det här problemet påverkar både snabb installation och anpassad installation. Den här ändringen löser problemet.

* Ett problem som orsakade fel söknings sidan i Azure AD Connects guiden att inte återges korrekt för administratörer som kör från Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* När du felsöker Lösenordssynkronisering med hjälp av sidan fel sökning i Azure AD Connect guiden, returnerar sidan fel sökning nu domän information.

* Om du tidigare försökte aktivera hash-synkronisering av lösen ord, kontrollerar Azure AD Connect inte om AD Connector-kontot har nödvändig behörighet för att synkronisera lösen ords hashar från lokala AD. Nu kommer Azure AD Connect guiden att verifiera och varna dig om AD Connector-kontot inte har tillräcklig behörighet.

### <a name="ad-fs-management"></a>AD FS hantering
#### <a name="fixed-issue"></a>Åtgärdat problem 
* Åtgärdat ett problem som rör användning av [MS-DS-ConsistencyGuid som käll fäst punkt](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) funktion. Det här problemet påverkar kunder som har konfigurerat *Federation med AD FS* som användar inloggnings metod. När du kör *Konfigurera käll fäst punkts* aktivitet i guiden, Azure AD Connect växlar till att använda * MS-DS-ConsistencyGuid som källattribut för immutableId. Som en del av den här ändringen försöker Azure AD Connect uppdatera anspråks reglerna för ImmutableId i AD FS. Det här steget misslyckades dock eftersom Azure AD Connect inte hade de autentiseringsuppgifter för administratören som krävs för att konfigurera AD FS. Med den här korrigeringen uppmanas du nu Azure AD Connect ange administratörs behörighet för AD FS när du kör aktiviteten *Konfigurera käll ankare* .



## <a name="116140"></a>1.1.614.0
Status: 05 2017 september

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Kända problem
* Det finns ett känt problem som orsakar Azure AD Connect uppgradering av fel meddelandet "*Det gick inte att uppgradera synkroniseringstjänsten*". Dessutom kan synkroniseringstjänsten inte längre starta med ett händelse fel:*Det gick inte att starta tjänsten eftersom databasens version är nyare än den installerade binärfilens version*. Problemet uppstår när administratören som utför uppgraderingen inte har sysadmin-behörighet till den SQL Server som används av Azure AD Connect. Dbo-behörigheter är inte tillräckliga.

* Det finns ett känt problem med Azure AD Connect uppgradering som påverkar kunder som har aktiverat [sömlös enkel inloggning](how-to-connect-sso.md). När Azure AD Connect har uppgraderats visas funktionen som inaktive rad i guiden, även om funktionen är aktive rad. En korrigering av det här problemet kommer att ges i framtida versioner. Kunder som är intresserade av detta visnings problem kan manuellt åtgärda det genom att aktivera sömlösa Sign-On i guiden.

#### <a name="fixed-issues"></a>Åtgärdade problem
* Ett problem har åtgärd ATS som förhindrade Azure AD Connect att uppdatera anspråks reglerna i lokala AD FS samtidigt [som funktionen ms-DS-ConsistencyGuid som käll-Anchor](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) aktiverades. Problemet uppstår om du försöker aktivera funktionen för en befintlig Azure AD Connect distribution som har AD FS kon figurer ATS som inloggnings metod. Problemet beror på att guiden inte begär ADFS-autentiseringsuppgifter innan du försöker uppdatera anspråks reglerna i AD FS.
* Åtgärdade ett problem som orsakade Azure AD Connect att Miss kunna installeras om den lokala AD-skogen har NTLM inaktiverat. Problemet beror på att Azure AD Connect guiden inte tillhandahåller fullständigt kvalificerade autentiseringsuppgifter när du skapar säkerhets kontexter som krävs för Kerberos-autentisering. Detta gör att Kerberos-autentiseringen Miss fungerar och Azure AD Connect guiden för att återgå till att använda NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Åtgärdade problem
* Ett problem har åtgärd ATS där det inte går att skapa en ny Synkroniseringsregel om attributet tagg inte har fyllts i.
* Ett problem som orsakade Azure AD Connect att ansluta till en lokal AD för Lösenordssynkronisering med NTLM har åtgärd ATS, trots att Kerberos är tillgängligt. Det här problemet uppstår om den lokala AD-topologin har en eller flera domänkontrollanter som har återställts från en säkerhets kopia.
* Ett problem har åtgärd ATS som gjorde att fullständig synkronisering av synkroniseringen sker i onödan efter uppgraderingen. I allmänhet krävs att du kör fullständiga synkroniseringar efter uppgraderingen om det finns ändringar i inaktuella regler för synkronisering. Problemet uppstod på grund av ett fel i logiken för ändrings identifiering som felaktigt identifierade en ändring vid inläsning av regel uttryck för synkronisering med rad matnings tecken. Rad matnings tecken infogas i regel uttryck för synkronisering för att förbättra läsbarheten.
* Ett problem har åtgärd ATS som kan orsaka att Azure AD Connects servern inte fungerar som den ska efter automatisk uppgradering. Det här problemet påverkar Azure AD Connect-servrar med version 1.1.443.0 (eller tidigare). Mer information om problemet finns i artikeln [Azure AD Connect fungerar inte korrekt efter en automatisk uppgradering](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Ett problem har åtgärd ATS som gör att automatisk uppgradering kan göras var 5: e minut när fel påträffas. Med den här korrigeringen försöker automatisk uppgradering igen med exponentiell säkerhets kopiering när fel påträffas.
* Ett problem har åtgärd ATS där lösen ords synkronisering händelse 611 visas felaktigt i Windows program händelse loggar som **information** i stället för **fel**. Händelse 611 genereras när Lösenordssynkronisering påträffar ett problem. 
* Ett problem har åtgärd ATS i guiden Azure AD Connect som tillåter att funktionen för tillbakaskrivning av grupp aktive ras utan att välja en ORGANISATIONSENHET som krävs för tillbakaskrivning av grupp.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Lade till en fel söknings uppgift i Azure AD Connect guiden under ytterligare aktiviteter. Kunderna kan utnyttja den här uppgiften för att felsöka problem som rör Lösenordssynkronisering och samla in allmän diagnostik. I framtiden kommer fel söknings aktiviteten att utökas till att omfatta andra problem som rör katalog synkronisering.
* Azure AD Connect har nu stöd för ett nytt installations läge som kallas **Använd befintlig databas**. Med det här installations läget kan kunder installera Azure AD Connect som anger en befintlig ADSync-databas. Mer information om den här funktionen finns i artikeln [använda en befintlig databas](how-to-connect-install-existing-database.md).
* För förbättrad säkerhet är Azure AD Connect nu standard att använda TLS 1.2 för att ansluta till Azure AD för Directory-synkronisering. Tidigare var standardvärdet TLS 1.0.
* När Azure AD Connect Lösenordssynkronisering startar, försöker den ansluta till den Azure AD-välkända slut punkten för Lösenordssynkronisering. Vid lyckad anslutning omdirigeras den till en landsspecifika slut punkt. Tidigare cachelagrar Lösenordssynkronisering den landsspecifika slut punkten tills den startas om. Nu rensar agenten cachen och försöker igen med den välkända slut punkten om den påträffar anslutnings problem med den landsspecifika slut punkten. Den här ändringen säkerställer att Lösenordssynkronisering kan redundansväxla till en annan landsspecifika slut punkt när den cachelagrade regions slut punkten inte längre är tillgänglig.
* Om du vill synkronisera ändringar från en lokal AD-skog krävs ett AD DS-konto. Du kan antingen (i) skapa AD DS-kontot själv och ange dess autentiseringsuppgifter för att Azure AD Connect eller (II) tillhandahålla företags administratörens autentiseringsuppgifter och låta Azure AD Connect skapa AD DS-kontot åt dig. Tidigare (i) är standard alternativet i guiden Azure AD Connect. Nu är standard alternativet (II).

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Stöd har lagts till för Microsoft Azure Government molnet och Microsoft Cloud Tyskland.

### <a name="ad-fs-management"></a>AD FS hantering
#### <a name="fixed-issues"></a>Åtgärdade problem
* Den Initialize-ADSyncNGCKeysWriteBack cmdleten i AD prep PowerShell-modulen har felaktigt tillämpat ACL: er för enhets registrerings behållaren och skulle därför bara ärva befintliga behörigheter.  Detta har uppdaterats så att Sync Service-kontot har rätt behörigheter.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Azure AD Connect verifiera att AD FS-inloggningen har uppdaterats så att den verifierar inloggningar mot Microsoft Online och inte bara för att hämta token från ADFS.
* När du konfigurerar en ny ADFS-servergrupp med hjälp av Azure AD Connect, flyttades sidan för ADFS-autentiseringsuppgifter så att den nu inträffar innan användaren uppmanas att tillhandahålla ADFS-och WAP-servrar.  Detta gör att Azure AD Connect kan kontrol lera att det angivna kontot har rätt behörigheter.
* Under Azure AD Connect uppgraderingen kommer vi inte längre att kunna uppgradera om AD FS Azure AD-förtroendet inte kan uppdateras.  Om detta inträffar visas ett lämpligt varnings meddelande i användaren och du bör fortsätta att återställa förtroendet via Azure AD Connect ytterligare uppgift.

### <a name="seamless-single-sign-on"></a>Sömlös enkel Sign-On
#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärdat ett problem som orsakade Azure AD Connect guiden att returnera ett fel om du försöker aktivera [sömlös enkel inloggning](how-to-connect-sso.md). Fel meddelandet är *"konfigurationen av Microsoft Azure AD Connect Authentication agent misslyckades."* Det här problemet påverkar befintliga kunder som manuellt har uppgraderat för hands versionen av autentiserings agenter för [direktautentisering](how-to-connect-sso.md) baserat på de steg som beskrivs i den här [artikeln](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Status: 23 2017 juli

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Åtgärdat problem 

* Ett problem som gjorde att den färdiga synkroniseringsregeln "ut till AD-User ImmutableId" togs bort:

  * Problemet uppstår när Azure AD Connect uppgraderas, eller när du uppdaterar konfiguration av *synkronisering* av Azure AD Connect i guiden Azure AD Connect.

  * Den här synkroniseringsregeln gäller för kunder som har aktiverat [funktionen ms-DS-ConsistencyGuid som käll-Anchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Den här funktionen introducerades i version 1.1.524.0 och senare. När synkroniseringsregeln tas bort kan Azure AD Connect inte längre fylla i det lokala AD ms-DS-ConsistencyGuid-attributet med attributvärdet ObjectGuid. Den förhindrar inte att nya användare tillhandahålls till Azure AD.

  * Korrigeringen säkerställer att synkroniseringsregeln inte längre kommer att tas bort under uppgraderingen eller under konfigurations ändringen, så länge funktionen är aktive rad. För befintliga kunder som har drabbats av det här problemet ser korrigeringen även till att synkroniseringsregeln läggs tillbaka efter uppgraderingen till den här versionen av Azure AD Connect.

* Ett problem har åtgärd ATS som medför att det finns prioritets värde som är mindre än 100:

  * Prioritets värden 0-99 är i allmänhet reserverade för anpassade regler för synkronisering. Under uppgraderingen uppdateras prioritets värden för out-of-Box-formateringsregler för att hantera ändringar i synkroniseringsregeln. På grund av det här problemet kan regler för direkt synkronisering tilldelas prioritets värde som är mindre än 100.

  * Korrigeringen förhindrar att problemet uppstår under uppgraderingen. Däremot återställs inte prioritets värden för befintliga kunder som har drabbats av problemet. En separat korrigering kommer att tillhandahållas i framtiden för att hjälpa till med återställningen.

* Ett problem har åtgärd ATS där [skärmen för domän-och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering) i guiden Azure AD Connect visar alternativet *synkronisera alla domäner och organisationsenheter* som är MARKERAT, även om ou-baserad filtrering är aktive rad.

*   Ett problem som orsakade [skärmen konfigurera katalogpartitioner](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) i Synchronization Service Manager att returnera ett fel om du klickar på knappen *Uppdatera* . Fel meddelandet är *"ett fel inträffade vid uppdatering av domäner: det går inte att konvertera objekt av typen" system. Collections. ArrayList "till typen" Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject "* . Felet uppstår när en ny AD-domän har lagts till i en befintlig AD-skog och du försöker uppdatera Azure AD Connect med hjälp av knappen Uppdatera.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* [Funktionen för automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) har utökats till stöd för kunder med följande konfigurationer:
  * Du har aktiverat funktionen tillbakaskrivning av enhet.
  * Du har aktiverat funktionen för tillbakaskrivning av grupp.
  * Installationen är inte en snabb inställning eller en DirSync-uppgradering.
  * Du har fler än 100 000 objekt i metaversum.
  * Du ansluter till fler än en skog. Express installation ansluter bara till en skog.
  * AD Connector-kontot är inte längre standard MSOL_s kontot.
  * Servern är inställt på att vara i mellanlagrings läge.
  * Du har aktiverat funktionen för tillbakaskrivning av användare.

  >[!NOTE]
  >Omfattnings expansionen för funktionen automatisk uppgradering påverkar kunder med Azure AD Connect build 1.1.105.0 och efter. Om du inte vill att din Azure AD Connect Server ska uppgraderas automatiskt måste du köra följande cmdlet på din Azure AD Connect-Server: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Mer information om hur du aktiverar/inaktiverar automatisk uppgradering finns i artikeln [Azure AD Connect: automatisk uppgradering](how-to-connect-install-automatic-upgrade.md).
## <a name="115580"></a>1.1.558.0
Status: kommer inte att släppas. Ändringar i den här versionen ingår i version 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Åtgärdat problem 

* Ett problem som gjorde att den färdiga synkroniseringsregeln "ut till AD-User-ImmutableId" har åtgärd ATS för borttagning när den OU-baserade filtrerings konfigurationen uppdateras. Den här synkroniseringsregeln krävs för [MS-DS-ConsistencyGuid som käll fäst punkt funktion](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Ett problem har åtgärd ATS där [skärmen för domän-och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering) i guiden Azure AD Connect visar alternativet *synkronisera alla domäner och organisationsenheter* som är MARKERAT, även om ou-baserad filtrering är aktive rad.

*   Ett problem som orsakade [skärmen konfigurera katalogpartitioner](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) i Synchronization Service Manager att returnera ett fel om du klickar på knappen *Uppdatera* . Fel meddelandet är *"ett fel inträffade vid uppdatering av domäner: det går inte att konvertera objekt av typen" system. Collections. ArrayList "till typen" Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject "* . Felet uppstår när en ny AD-domän har lagts till i en befintlig AD-skog och du försöker uppdatera Azure AD Connect med hjälp av knappen Uppdatera.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* [Funktionen för automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) har utökats till stöd för kunder med följande konfigurationer:
  * Du har aktiverat funktionen tillbakaskrivning av enhet.
  * Du har aktiverat funktionen för tillbakaskrivning av grupp.
  * Installationen är inte en snabb inställning eller en DirSync-uppgradering.
  * Du har fler än 100 000 objekt i metaversum.
  * Du ansluter till fler än en skog. Express installation ansluter bara till en skog.
  * AD Connector-kontot är inte längre standard MSOL_s kontot.
  * Servern är inställt på att vara i mellanlagrings läge.
  * Du har aktiverat funktionen för tillbakaskrivning av användare.

  >[!NOTE]
  >Omfattnings expansionen för funktionen automatisk uppgradering påverkar kunder med Azure AD Connect build 1.1.105.0 och efter. Om du inte vill att din Azure AD Connect Server ska uppgraderas automatiskt måste du köra följande cmdlet på din Azure AD Connect-Server: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Mer information om hur du aktiverar/inaktiverar automatisk uppgradering finns i artikeln [Azure AD Connect: automatisk uppgradering](how-to-connect-install-automatic-upgrade.md).
## <a name="115570"></a>1.1.557.0
Status: 2017 juli

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder med hjälp av funktionen för automatisk uppgradering av Azure AD Connect.
### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Åtgärdat problem 
* Ett problem har åtgärd ATS med Initialize-ADSyncDomainJoinedComputerSync-cmdleten som gjorde att den verifierade domänen har kon figurer ATS på det befintliga tjänst anslutnings punktens objekt som ska ändras även om det fortfarande är en giltig domän. Det här problemet uppstår när din Azure AD-klient har fler än en verifierad domän som kan användas för att konfigurera tjänst anslutnings punkten.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Tillbakaskrivning av lösen ord är nu tillgängligt för förhands granskning med Microsoft Azure Government Cloud och Microsoft Cloud Tyskland. Mer information om Azure AD Connect stöd för de olika tjänst instanserna finns i artikeln [Azure AD Connect: särskilda överväganden för instanser](reference-connect-instances.md).

* Initialize-ADSyncDomainJoinedComputerSync cmdlet har nu en ny valfri parameter med namnet AzureADDomain. Med den här parametern kan du ange vilken verifierad domän som ska användas för att konfigurera tjänst anslutnings punkten.

### <a name="pass-through-authentication"></a>Direktautentisering

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Namnet på den agent som krävs för direktautentisering har ändrats från *Microsoft Azure AD Application Proxy Connector* till *Microsoft Azure AD Connect Authentication agent*.

* Genom att aktivera direkt autentisering aktive ras inte längre Lösenordssynkronisering som standard.


## <a name="115530"></a>1.1.553.0
Status: 2017 juni

> [!IMPORTANT]
> Det finns ändringar i schema-och Synkroniseringsregel som introducerades i den här versionen. Azure AD Connect synkroniseringstjänsten utlöser fullständig import och fullständig synkronisering efter uppgraderingen. Information om ändringarna beskrivs nedan. Om du vill skjuta upp fullständig import och fullständig synkronisering efter uppgraderingen läser du artikeln [hur du skjuter upp fullständig synkronisering efter uppgraderingen](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>
### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Kända problem
* Det finns ett problem som påverkar kunder som använder [OU-baserad filtrering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) med Azure AD Connect Sync. När du navigerar till [sidan domän-och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering) i guiden Azure AD Connect, förväntas följande beteende:
  * Om OU-baserad filtrering har Aktiver ATS väljs alternativet **Synkronisera valda domäner och organisationsenheter** .
  * Annars väljs alternativet **synkronisera alla domäner och organisationsenheter** .

Problemet som uppstår är att **alternativet Synkronisera alla domäner och organisationsenheter** alltid är markerat när du kör guiden.  Detta inträffar även om OU-baserad filtrering tidigare har kon figurer ATS. Innan du sparar Azure AD Connect konfigurations ändringar kontrollerar du att **alternativet Synkronisera valda domäner och OU är markerat** och bekräftar att alla organisationsenheter som behöver synkroniseras har Aktiver ATS igen. Annars kommer OU-baserad filtrering att inaktive ras.

#### <a name="fixed-issues"></a>Åtgärdade problem

* Ett problem har åtgärd ATS med tillbakaskrivning av lösen ord som gör det möjligt för en Azure AD-administratör att återställa lösen ordet för ett lokalt AD-privilegierat användar konto. Problemet uppstår när Azure AD Connect beviljas behörighet att återställa lösen ordet över det privilegierade kontot. Problemet behandlas i den här versionen av Azure AD Connect genom att inte tillåta att en Azure AD-administratör återställer lösen ordet för ett godtyckligt lokalt AD-privilegierat användar konto, om inte administratören är ägare till kontot. Mer information finns i [säkerhets meddelande 4033453](/security-updates/SecurityAdvisories/2017/4033453).

* Åtgärdat ett problem som rör funktionen [MS-DS-ConsistencyGuid som käll ankare](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) där Azure AD Connect inte ändrar till ett lokalt AD ms-DS-ConsistencyGuid-attribut. Problemet uppstår när flera lokala AD-skogar läggs till Azure AD Connect och *användar identiteter finns över flera kataloger* är markerat. När en sådan konfiguration används fyller inte de resulterande reglerna för synkronisering i sourceAnchorBinary-attributet i metaversum. Attributet sourceAnchorBinary används som source-attribut för ms-DS-ConsistencyGuid-attributet. Därför sker inte tillbakaskrivning av MS-DSConsistencyGuid-attributet. För att åtgärda problemet har följande regler för synkronisering uppdaterats för att säkerställa att attributet sourceAnchorBinary i metaversum alltid är ifyllt:
  * I från AD-InetOrgPerson AccountEnabled.xml
  * I från AD-InetOrgPerson Common.xml
  * I från AD-User AccountEnabled.xml
  * I från AD-User Common.xml
  * I från AD-User Join SOAInAAD.xml

* Även om [MS-DS-ConsistencyGuid som käll fäst punkt](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) inte är aktive rad, läggs fortfarande synkroniseringsregeln till Azure AD Connect. Påverkan är oskadlig och gör inte att tillbakaskrivning av ms-DS-ConsistencyGuid-attribut inträffar. För att undvika förvirring har logik lagts till för att säkerställa att synkroniseringsregeln bara läggs till när funktionen är aktive rad.

* Ett problem har åtgärd ATS som orsakade synkronisering av lösen ords-hash med fel händelse 611. Det här problemet uppstår efter att en eller flera domänkontrollanter har tagits bort från den lokala AD-platsen. I slutet av varje omsynkronisering av lösen ord innehåller synkroniserings-cookien som utfärdats av den lokala AD-platsen anrops-ID: n för de borttagna domän kontrol Lanterna med USN (Update Sequence Number) värdet 0. Synkroniseringshanteraren för lösen ord kan inte spara cookien för synkronisering som innehåller USN-värdet 0 och Miss lyckas med fel händelse 611. Under nästa synkronisering återanvänder hanteraren för Lösenordssynkronisering den senast sparade cookien för synkronisering som inte innehåller USN-värdet 0. Detta gör att samma lösen ords ändringar omsynkroniseras. Med den här korrigerings filen behåller Synkroniseringshanteraren för Lösenordssynkronisering korrekt.

* Även om automatisk uppgradering har inaktiverats med hjälp av Set-ADSyncAutoUpgrade cmdleten, fortsätter den automatiska uppgraderings processen att söka efter uppgraderingen regelbundet och förlitar sig på det nedladdade installations programmet för att följa inaktive ring. Med den här korrigeringen söker den automatiska uppgraderings processen inte längre efter uppgradering med jämna mellanrum. Korrigeringen tillämpas automatiskt när uppgradering av installations programmet för den här Azure AD Connect versionen utförs en gång.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Tidigare var [MS-DS-ConsistencyGuid as-funktionen för käll ankare](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) bara tillgänglig för nya distributioner. Nu är den tillgänglig för befintliga distributioner. Mer specifikt:
  * För att få åtkomst till funktionen startar du guiden Azure AD Connect och väljer alternativet för *uppdatering av käll ankare* .
  * Det här alternativet visas bara för befintliga distributioner som använder attributet objectGuid som sourceAnchor.
  * När du konfigurerar alternativet, verifierar guiden statusen för ms-DS-ConsistencyGuid-attributet i din lokala Active Directory. Om attributet inte har kon figurer ATS för något användar objekt i katalogen använder guiden ms-DS-ConsistencyGuid som sourceAnchor-attribut. Om attributet har kon figurer ATS för ett eller flera användar objekt i katalogen, avslutar guiden att attributet används av andra program och inte är lämpligt som sourceAnchor-attribut och tillåter inte att käll ankaret ändras för att fortsätta. Om du är säker på att attributet inte används av befintliga program måste du kontakta supporten om du vill ha information om hur du ignorerar felet.

* Endast för **userCertificate** -attribut på enhets objekt, Azure AD Connect nu söker efter certifikat värden [som krävs för att ansluta domänanslutna enheter till Azure AD för Windows 10-upplevelse](../devices/hybrid-azuread-join-plan.md) och filtrera bort resten innan du synkroniserar med Azure AD. För att aktivera det här beteendet har den färdiga synkroniseringsregeln "ut till AAD-Device Join-SOAInAD" uppdaterats.

* Azure AD Connect stöder nu tillbakaskrivning av Exchange Online **cloudPublicDelegates** -attributet till ett lokalt AD **publicDelegates** -attribut. Detta aktiverar scenariot där en Exchange Online-postlåda kan beviljas SendOnBehalfTo-rättigheter till användare med lokal Exchange-postlåda. För att stödja den här funktionen har en ny regel för att synkronisera hybrid-PublicDelegates "ut till AD – användare Exchange hybrid" lagts till. Den här synkroniseringsregeln läggs endast till Azure AD Connect när funktionen Exchange hybrid är aktive rad.

* Azure AD Connect stöder nu synkronisering av attributet **altRecipient** från Azure AD. Om du vill ha stöd för den här ändringen har du uppdaterat följande regler för synkronisering av attribut som krävs för den här ändringen:
  * I från AD – användar utbyte
  * Ut till AAD – User ExchangeOnline

* Attributet **cloudSOAExchMailbox** i metaversum anger om en specifik användare har Exchange Online-postlåda eller inte. Dess definition har uppdaterats till att omfatta ytterligare Exchange Online-RecipientDisplayTypes som sådan utrustning och konferens rummets post lådor. För att aktivera den här ändringen, har definitionen av attributet cloudSOAExchMailbox, som finns under den färdiga synkroniseringsregeln "i från AAD – användare Exchange hybrid", uppdaterats från:

    ```
    CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
    ```

    ... till följande:

    ```
    CBool(
      IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
        IIF([cloudMSExchRecipientDisplayType]=0,True,(
          IIF([cloudMSExchRecipientDisplayType]=2,True,(
            IIF([cloudMSExchRecipientDisplayType]=7,True,(
              IIF([cloudMSExchRecipientDisplayType]=8,True,(
                IIF([cloudMSExchRecipientDisplayType]=10,True,(
                  IIF([cloudMSExchRecipientDisplayType]=16,True,(
                    IIF([cloudMSExchRecipientDisplayType]=17,True,(
                      IIF([cloudMSExchRecipientDisplayType]=18,True,(
                        IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                          IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))
    ```

* Följande uppsättning X509Certificate2-kompatibla funktioner har lagts till för att skapa regler för Synkroniseringsregel för att hantera certifikat värden i attributet userCertificate:
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertSubjectNameDN
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * IsCert
  * CertFriendlyName
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * CertSerialNumber
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * Välj
  * CertKeyAlgorithmParams
  * CertHashString
  * Var
  * With

* Följande schema ändringar har införts så att kunderna kan skapa anpassade regler för att flöda sAMAccountName, domainNetBios och domainFQDN för grupp objekt, samt distinguishedName för användar objekt:

  * Följande attribut har lagts till i MV-schemat:
    * Grupp: AccountName
    * Grupp: domainNetBios
    * Grupp: domainFQDN
    * Person: distinguishedName

  * Följande attribut har lagts till i Azure AD Connector-schemat:
    * Grupp: egna namnet onpremisessamaccountname
    * Grupp: NetBiosName
    * Grupp: DnsDomainName
    * Användare: OnPremisesDistinguishedName

* ADSyncDomainJoinedComputerSync-cmdlet-skriptet har nu en ny valfri parameter med namnet AzureEnvironment. Parametern används för att ange vilken region motsvarande Azure Active Directory klient organisation finns i. Giltiga värden är:
  * AzureCloud (standard)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment

* Uppdaterade regel redigeraren för synkronisering för att använda Join (i stället för att etablera) som standardvärdet för länk typen vid skapande av Synkroniseringsregel.

### <a name="ad-fs-management"></a>AD FS hantering

#### <a name="issues-fixed"></a>Korrigerade problem

* Följande URL: er är nya WS-Federation-slutpunkter som introducerats av Azure AD för att förbättra återhämtning mot autentiseringsfel och kommer att läggas till lokalt AD FS konfiguration för svars partens förtroende:
  * https: \/ /ests.login.microsoftonline.com/login.srf
  * https: \/ /stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf

* Ett problem som orsakade AD FS att generera ett felaktigt anspråks värde för IssuerID har åtgärd ATS. Problemet uppstår om det finns flera verifierade domäner i Azure AD-klienten och domänsuffixet för det userPrincipalName-attribut som används för att generera IssuerID-anspråk är minst 3 nivåer djup (till exempel johndoe@us.contoso.com ). Problemet löses genom att det regex som används av anspråks reglerna uppdateras.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Tidigare kan ADFS-funktionen för certifikat hantering som tillhandahålls av Azure AD Connect endast användas med ADFS-grupper som hanteras via Azure AD Connect. Nu kan du använda funktionen med ADFS-grupper som inte hanteras med hjälp av Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Lanserad: maj 2017

> [!IMPORTANT]
> Det finns ändringar i schema-och Synkroniseringsregel som introducerades i den här versionen. Azure AD Connect synkroniseringstjänsten aktiverar fullständig import och fullständig synkronisering efter uppgraderingen. Information om ändringarna beskrivs nedan.
>
>
**Korrigerade problem:**

Azure AD Connect-synkronisering

* Ett problem har åtgärd ATS som innebär att automatisk uppgradering sker på Azure AD Connect servern även om kunden har inaktiverat funktionen med hjälp av Set-ADSyncAutoUpgrade-cmdleten. Med den här korrigeringen söker den automatiska uppgraderings processen på servern fortfarande efter uppgradering med jämna mellanrum, men det nedladdade installations programmet följer konfigurationen för automatisk uppgradering.
* Vid uppdatering av DirSync på plats skapar Azure AD Connect ett Azure AD-tjänstkontot som ska användas av Azure AD-anslutaren för synkronisering med Azure AD. När kontot har skapats autentiseras Azure AD Connect med Azure AD med hjälp av kontot. Autentiseringen Miss lyckas ibland på grund av tillfälliga problem, vilket i sin tur gör att DirSync-uppgraderingen på plats Miss lyckas med felet *"ett fel uppstod vid körning av konfigurera AAD Sync aktivitet: AADSTS50034: om du vill logga in i det här programmet måste kontot läggas till i xxx.onmicrosoft.com-katalogen."* För att förbättra återhämtningen av DirSync-uppgraderingen försöker Azure AD Connect nu autentisera steget igen.
* Det uppstod ett problem med build 443 som gör att DirSync-uppgraderingen på plats lyckas men körnings profiler som krävs för katalog synkronisering skapas inte. Lagnings logik ingår i den här versionen av Azure AD Connect. När kunden uppgraderar till den här versionen identifierar Azure AD Connect saknade körnings profiler och skapar dem.
* Ett problem har åtgärd ATS som medför att processen för Lösenordssynkronisering inte startar med händelse-ID 6900 och fel *"ett objekt med samma nyckel har redan lagts till"*. Det här problemet uppstår om du uppdaterar konfigurationen av OU-filtrering för att inkludera AD-Konfigurationsprovider. För att lösa det här problemet synkroniserar processen för Lösenordssynkronisering nu lösen ords ändringar från AD-domänsuffix. Partitioner som inte tillhör en domän, till exempel en konfigurations partition, hoppas över.
* Under snabb installationen skapar Azure AD Connect ett lokalt AD DS-konto som ska användas av AD-anslutningen för att kommunicera med lokala AD. Tidigare skapas kontot med flaggan PASSWD_NOTREQD som angetts för attributet User-Account-Control och ett slumpmässigt lösen ord anges för kontot. Azure AD Connect tar nu bort PASSWD_NOTREQD flagga när lösen ordet har angetts för kontot.
* Ett problem har åtgärd ATS som medför att DirSync-uppgraderingen Miss känner till felet *"ett död läge inträffade i SQL Server som försöker hämta ett program lås"* när attributet "smek namn" finns i det lokala AD-schemat, men inte har bundits till AD User Object-klassen.
* Ett problem har åtgärd ATS som medför att funktionen tillbakaskrivning av enhet automatiskt inaktive ras när en administratör uppdaterar Azure AD Connect Sync-konfiguration med hjälp av guiden Azure AD Connect. Det här problemet orsakas av att guiden utför en krav kontroll för den befintliga tillbakaskrivning av enhets konfiguration i lokal AD och kontrollen Miss lyckas. Korrigeringen är att hoppa över kontrollen om tillbakaskrivning av enheten redan har Aktiver ATS.
* Om du vill konfigurera ORGANISATIONSENHETs filtrering kan du antingen använda guiden Azure AD Connect eller Synchronization Service Manager. Om du tidigare använder guiden Azure AD Connect för att konfigurera ORGANISATIONSENHETs filtrering, inkluderas nya organisationsenheter som skapats senare i Active Directory-synkronisering. Om du inte vill att nya organisationsenheter ska ingå måste du konfigurera ORGANISATIONSENHETs filtrering med hjälp av Synchronization Service Manager. Nu kan du få samma beteende med Azure AD Connect guiden.
* Ett problem har åtgärd ATS som innebär att lagrade procedurer som krävs av Azure AD Connect skapas under schemat för den installerande administratören, i stället för under dbo-schemat.
* Ett problem har åtgärd ATS som medför att attributet TrackingId som returneras av Azure AD utelämnas i händelse loggarna för Azure AD Connect servern. Problemet uppstår om Azure AD Connect tar emot ett omdirigerings meddelande från Azure AD och Azure AD Connect inte kan ansluta till den angivna slut punkten. TrackingId används av support tekniker för att korrelera med service Side-loggar under fel sökning.
* När Azure AD Connect tar emot LargeObject-fel från Azure AD, genererar Azure AD Connect en händelse med EventID 6941 och meddelandet *"det etablerade objektet är för stort. Rensa antalet attributvärden för det här objektet. "* Samtidigt genererar Azure AD Connect också en vilseledande händelse med EventID 6900 och meddelandet *"Microsoft. online. samexistens. ProvisionRetryException: det går inte att kommunicera med tjänsten Windows Azure Active Directory".* För att minimera förvirring genererar Azure AD Connect inte längre den sistnämnda händelsen när LargeObject-fel tas emot.
* Ett problem har åtgärd ATS som medför att Synchronization Service Manager slutar svara vid försök att uppdatera konfigurationen för en allmän LDAP-anslutning.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Synkronisera regel ändringar – följande ändringar av synkroniseringsregeln har implementerats:
  * Den uppdaterade standard regel uppsättningen för synkronisering är inte exportera attributen **userCertificate** och **userSMIMECertificate** om attributen har fler än 15 värden.
  * AD-attributen **Anställningsnr** och **msExchBypassModerationLink** ingår nu i standard regel uppsättningen för synkronisering.
  * **Fotot** för AD-attribut har tagits bort från standard regel uppsättningen för synkronisering.
  * Lade till **preferredDataLocation** till metaversum-schemat och Azure AD Connector-schemat. Kunder som vill uppdatera antingen attribut i Azure AD kan implementera anpassade regler för synkronisering. 
  * Lade till **userType** till metaversum-schemat och Azure AD Connector-schemat. Kunder som vill uppdatera antingen attribut i Azure AD kan implementera anpassade regler för synkronisering.

* Azure AD Connect aktiverar nu automatiskt attributet ConsistencyGuid som källans Anchor-attribut för lokala AD-objekt. Vidare fyller Azure AD Connect attributet ConsistencyGuid med värdet objectGuid om det är tomt. Den här funktionen gäller endast för nya distributioner. Mer information om den här funktionen finns i artikel avsnitt [Azure AD Connect: utforma koncept – använda MS-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* En ny fel söknings-cmdlet Invoke-ADSyncDiagnostics har lagts till för att diagnostisera problem med hash-synkronisering för lösen ord. Information om hur du använder-cmdleten finns i artikeln [Felsöka Lösenordssynkronisering med Azure AD Connect Sync](tshoot-connect-password-hash-synchronization.md).
* Azure AD Connect stöder nu synkronisering Mail-Enabled offentliga mappobjekt från lokala AD till Azure AD. Du kan aktivera funktionen med Azure AD Connect guiden under valfria funktioner. Mer information om den här funktionen finns i artikeln [Office 365 Directory-baserad Edge-blockering för lokala e-postmappar med lokala e-postfunktioner](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218).
* Azure AD Connect kräver ett AD DS-konto för att synkronisera från lokal AD. Om du tidigare har installerat Azure AD Connect med Express läget kan du ange autentiseringsuppgifterna för ett företags administratörs konto och Azure AD Connect skapa det AD DS-konto som krävs. Men för en anpassad installation och för att lägga till skogar i en befintlig distribution, var du tvungen att ange AD DS-kontot i stället. Nu kan du också välja att ange autentiseringsuppgifterna för ett företags administratörs konto under en anpassad installation och låta Azure AD Connect skapa det AD DS-konto som krävs.
* Azure AD Connect stöder nu SQL-AOA. Du måste aktivera SQL-AOA innan du installerar Azure AD Connect. Under installationen identifierar Azure AD Connect om den angivna SQL-instansen är aktive rad för SQL-AOA eller inte. Om SQL-AOA är aktive rad kan Azure AD Connect ytterligare siffror om SQL-AOA har kon figurer ATS för att använda synkron replikering eller asynkron replikering. När du konfigurerar tillgänglighets gruppens lyssnare rekommenderar vi att du ställer in egenskapen RegisterAllProvidersIP på 0. Den här rekommendationen beror på att Azure AD Connect för närvarande använder SQL Native Client för att ansluta till SQL och SQL Native Client inte stöder användning av egenskapen MultiSubNetFailover.
* Om du använder LocalDB som databas för din Azure AD Connect Server och har nått gränsen på 10 GB, startar inte längre synkroniseringstjänsten. Tidigare var du tvungen att utföra ShrinkDatabase-åtgärden på LocalDB för att frigöra tillräckligt med DB-utrymme för att synkroniseringstjänsten ska starta. Sedan kan du använda Synchronization Service Manager för att ta bort körnings historiken för att frigöra mer databas utrymme. Nu kan du använda Start-ADSyncPurgeRunHistory cmdlet för att rensa kör historik data från LocalDB för att frigöra databas utrymme. Dessutom stöder denna cmdlet ett offline-läge (genom att ange parametern-offline) som kan användas när synkroniseringstjänsten inte körs. Obs: offline-läget kan bara användas om synkroniseringstjänsten inte körs och databasen som används är LocalDB.
* För att minska den mängd lagrings utrymme som krävs kan Azure AD Connect nu komprimera synkroniseringsfel innan de lagras i LocalDB/SQL-databaser. När du uppgraderar från en äldre version av Azure AD Connect till den här versionen kan Azure AD Connect utföra en engångs komprimering på befintliga synkroniseringsfel.
* När du tidigare har uppdaterat konfigurationen av OU-filtrering måste du köra fullständig import manuellt för att se till att befintliga objekt inkluderas/undantas från katalog synkroniseringen. Nu utlöser Azure AD Connect automatiskt fullständig import under nästa synkronisering. Dessutom tillämpas fullständig import endast på de AD-anslutningar som påverkas av uppdateringen. OBS! den här förbättringen gäller endast för ORGANISATIONSENHETs filtrerings uppdateringar som har skapats med hjälp av guiden Azure AD Connect. Det går inte att använda organisations filtrerings uppdateringen som gjorts med Synchronization Service Manager.
* Grupp-baserad filtrering stöder tidigare endast användare, grupper och kontakt objekt. Grupp-baserad filtrering stöder nu även dator objekt.
* Tidigare kan du ta bort kopplings utrymmes data utan att inaktivera Azure AD Connect Sync Scheduler. Nu blockerar Synchronization Service Manager borttagningen av kopplings utrymmes data om den upptäcker att Scheduler är aktive rad. Vidare returneras en varning som informerar kunder om eventuell data förlust om kopplings utrymmes data tas bort.
* Tidigare måste du inaktivera PowerShell-avskrift för att Azure AD Connect guiden ska kunna köras korrekt. Det här problemet har delvis lösts. Du kan aktivera PowerShell-avskriftning om du använder Azure AD Connects guiden för att hantera synkronisering av konfigurationen. Du måste inaktivera PowerShell-avskrift om du använder guiden Azure AD Connect för att hantera ADFS-konfiguration.



## <a name="114860"></a>1.1.486.0
Lanserad: april 2017

**Korrigerade problem:**
* Ett problem har åtgärd ATS där Azure AD Connect inte kan installeras på den lokaliserade versionen av Windows Server.

## <a name="114840"></a>1.1.484.0
Lanserad: april 2017

**Kända problem:**

* Den här versionen av Azure AD Connect installeras inte korrekt om följande villkor är uppfyllda:
   1. Du utför en uppgradering på plats eller en ny installation av Azure AD Connect.
   2. Du använder en lokaliserad version av Windows Server där namnet på den inbyggda administratörs gruppen på servern inte är "administratörer".
   3. Du använder standard SQL Server 2012 Express-LocalDB som installerats med Azure AD Connect i stället för att tillhandahålla en egen fullständig SQL.

**Korrigerade problem:**

Azure AD Connect-synkronisering
* Ett problem har åtgärd ATS där Sync Scheduler hoppar över hela Sync-steget om en eller flera kopplingar saknar körnings profil för det steget. Till exempel har du lagt till en anslutning manuellt med hjälp av Synchronization Service Manager utan att skapa en delta-import körnings profil för den. Den här korrigeringen säkerställer att Sync Scheduler fortsätter att köra delta import för andra anslutningar.
* Ett problem har åtgärd ATS där synkroniseringstjänsten omedelbart slutar bearbeta en körnings profil när den påträffar ett problem med ett av körnings stegen. Den här korrigeringen säkerställer att synkroniseringstjänsten hoppar över det steget och fortsätter att bearbeta resten. Du kan till exempel ha en delta-import-profil för din AD-anslutning med flera körnings steg (en för varje lokal AD-domän). Synkroniseringstjänsten kör delta import med andra AD-domäner även om någon av dem har problem med nätverks anslutningen.
* Ett problem har åtgärd ATS som gör att Azure AD Connector-uppdateringen hoppas över under automatisk uppgradering.
* Ett problem har åtgärd ATS som gör att Azure AD Connect felaktigt avgör om servern är en domänkontrollant under installationen, vilket i sin tur gör att DirSync-uppgraderingen inte kan slutföras.
* Ett problem som gör att DirSync-uppgraderingen på plats inte skapar någon körnings profil för Azure AD-anslutningen har åtgärd ATS.
* Ett problem har åtgärd ATS där Synchronization Service Manager användar gränssnitt slutar svara vid försök att konfigurera en allmän LDAP-anslutning.

AD FS hantering
* Ett problem har åtgärd ATS där Azure AD Connects guiden Miss lyckas om den AD FS primära noden har flyttats till en annan server.

Enkel inloggning på Skriv bordet
* Ett problem har åtgärd ATS i guiden Azure AD Connect där Sign-In skärmen inte tillåter att du aktiverar funktionen för enkel inloggning på Skriv bordet om du väljer Lösenordssynkronisering som Sign-In alternativ under den nya installationen.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Azure AD Connect Sync stöder nu användning av virtuellt tjänst konto, hanterat tjänst konto och grupphanterat tjänst konto som tjänst konto. Detta gäller endast för nya installationer av Azure AD Connect. När du installerar Azure AD Connect:
    * Som standard skapar guiden Azure AD Connect ett virtuellt tjänst konto och använder det som tjänst konto.
    * Om du installerar på en domänkontrollant går Azure AD Connect tillbaka till föregående beteende där den skapar ett domän användar konto och använder det som sitt tjänst konto i stället.
    * Du kan åsidosätta standard beteendet genom att ange något av följande:
      * Ett grupphanterat tjänst konto
      * Ett hanterat tjänst konto
      * Ett domän användar konto
      * Ett lokalt användar konto
* Om du tidigare uppgraderar till en ny version av Azure AD Connect som innehåller anslutnings uppdateringar eller synkroniserade regel ändringar, utlöses Azure AD Connect en fullständig synkronisering. Nu är Azure AD Connect selektivt utlösare för fullständig import endast för anslutningar med uppdatering och fullständig synkronisering endast för kopplingar med regler för synkronisering av synkronisering.
* Tidigare gäller tröskelvärdet för export borttagning endast för exporter som utlöses via Sync Scheduler. Nu utökas funktionen till att inkludera exporter som har utlösts manuellt av kunden med hjälp av Synchronization Service Manager.
* På din Azure AD-klient finns det en tjänst konfiguration som anger om funktionen för Lösenordssynkronisering är aktive rad för din klient organisation eller inte. Tidigare är det enkelt att konfigurera tjänst konfigurationen på ett felaktigt sätt genom att Azure AD Connect när du har en aktiv och en fristående server. Azure AD Connect försöker nu att behålla tjänst konfigurationen som är konsekvent med din aktiva Azure AD Connect-Server.
* Azure AD Connects guiden identifierar och returnerar nu en varning om AD-pappers korgen inte är aktive rad på den lokala AD-platsen.
* Exporterades tidigare till Azure AD-timeout och Miss lyckas om den kombinerade storleken på objekten i batchen överskrider vissa tröskelvärden. Nu försöker synkroniseringstjänsten igen att skicka om objekten i separata, mindre batchar om problemet uppstår.
* Key Management-programmet för synkroniseringstjänsten har tagits bort från Start-menyn i Windows. Hantering av krypterings nyckel fortsätter att stödjas via kommando rads gränssnittet med hjälp av miiskmu.exe. Information om hur du hanterar krypterings nyckeln finns i artikeln om [att överge Azure AD Connect krypterings nyckel för synkronisering](./how-to-connect-sync-change-serviceacct-pass.md#abandoning-the-adsync-service-account-encryption-key).
* Tidigare, om du ändrar lösen ordet för Azure AD Connect Sync-tjänstkontot, kan inte synkroniseringstjänsten starta korrekt förrän du har övergivit krypterings nyckeln och initierat lösen ordet för Azure AD Connect Sync-tjänstkontot. Nu krävs inte längre den här processen.

Enkel inloggning på Skriv bordet

* Azure AD Connect guiden kräver inte längre port 9090 för att kunna öppnas i nätverket när du konfigurerar direktautentisering och skriv bords inloggning. Endast port 443 krävs.

## <a name="114430"></a>1.1.443.0
Lanserad: mars 2017

**Korrigerade problem:**

Azure AD Connect-synkronisering
* Ett Azure AD Connect problem har åtgärd ATS om att visnings namnet för Azure AD-anslutningen inte innehåller den inledande onmicrosoft.com-domän som tilldelats till Azure AD-klienten.
* Ett problem har åtgärd ATS som gör att Azure AD Connects guiden inte kan ansluta till SQL Database när lösen ordet för kontot Sync-tjänst innehåller specialtecken, till exempel apostrof, kolon och blank steg.
* Ett problem har åtgärd ATS som orsakar felet "bilden har ett annat ankare än avbildningen som ska inträffa på en Azure AD Connect-Server i mellanlagrings läge, efter att du tillfälligt har uteslutit ett lokalt AD-objekt från synkronisering och sedan inkluderat det igen för synkronisering.
* Åtgärdat ett problem som orsakar felet "objektet som identifieras av DN är en fiktiv" som inträffar på en Azure AD Connect-Server i mellanlagrings läge, efter att du tillfälligt har uteslutit ett lokalt AD-objekt från synkronisering och sedan inkluderat det igen för synkronisering.

AD FS hantering
* Ett problem har åtgärd ATS där Azure AD Connect guiden inte uppdaterar AD FS konfiguration och ställer in rätt anspråk på den förlitande partens förtroende efter att alternativt inloggnings-ID har kon figurer ATS.
* Ett problem har åtgärd ATS där Azure AD Connects guiden inte kan hantera AD FS servrar vars tjänst konton har kon figurer ATS med userPrincipalName-format i stället för sAMAccountName-format.

Direktautentisering
* Ett problem har åtgärd ATS, vilket gör att Azure AD Connects guide Miss lyckas om direktautentisering är markerat men registreringen av anslutnings programmet Miss lyckas.
* Ett problem har åtgärd ATS som gör att en Azure AD Connects guide kringgår verifierings kontroller för inloggnings metoden som väljs när funktionen för enkel inloggning är aktive rad

Lösenordsåterställning
* Ett problem som kan orsaka att Azure Azure AD Connect-servern inte försöker ansluta igen om anslutningen stoppades av en brand vägg eller proxy.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Get-ADSyncScheduler cmdlet returnerar nu en ny boolesk egenskap med namnet SyncCycleInProgress. Om det returnerade värdet är sant innebär det att en schemalagd synkronisering pågår.
* Målmappen för att lagra Azure AD Connect installations-och installations loggar har flyttats från%localappdata%\AADConnect till%programdata%\AADConnect för att förbättra åtkomsten till loggfilerna.

AD FS hantering
* Stöd har lagts till för uppdatering av AD FS server grupps TLS/SSL-certifikat.
* Stöd har lagts till för hantering av AD FS 2016.
* Nu kan du ange befintliga gMSA (grupphanterat tjänst konto) under AD FS installationen.
* Du kan nu konfigurera SHA-256 som signatur-hash-algoritmen för förtroende för Azure AD-förlitande part.

Lösenordsåterställning
* Introducerade förbättringar för att tillåta att produkten fungerar i miljöer med mer strikta brand Väggs regler.
* Bättre anslutnings tillförlitlighet till Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Lanserad: december 2016

**Åtgärdat problem:**

* Ett problem har åtgärd ATS där issuerid-anspråks regeln för Active Directory Federation Services (AD FS) (AD FS) saknas i den här versionen.

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder med hjälp av funktionen för automatisk uppgradering av Azure AD Connect.
## <a name="113710"></a>1.1.371.0
Lanserad: december 2016

**Känt problem:**

* Issuerid-anspråks regeln för AD FS saknas i den här versionen. Issuerid-anspråks regeln krävs om du federerar flera domäner med Azure Active Directory (Azure AD). Om du använder Azure AD Connect för att hantera den lokala AD FS distributionen tar uppgraderingen av den här versionen bort den befintliga issuerid-anspråks regeln från din AD FS konfiguration. Du kan lösa problemet genom att lägga till issuerid-anspråks regeln efter installationen/uppgraderingen. Mer information om hur du lägger till issuerid-anspråks regeln finns i den här artikeln om [flera domän stöd för federering med Azure AD](how-to-connect-install-multiple-domains.md).

**Åtgärdat problem:**

* Om Port 9090 inte är öppen för utgående anslutning, Miss lyckas installationen eller uppgraderingen av Azure AD Connect.

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder med hjälp av funktionen för automatisk uppgradering av Azure AD Connect.
## <a name="113700"></a>1.1.370.0
Lanserad: december 2016

**Kända problem:**

* Issuerid-anspråks regeln för AD FS saknas i den här versionen. Issuerid-anspråks regeln krävs om du federerar flera domäner med Azure AD. Om du använder Azure AD Connect för att hantera den lokala AD FS distributionen tar uppgraderingen av den här versionen bort den befintliga issuerid-anspråks regeln från din AD FS konfiguration. Du kan lösa problemet genom att lägga till issuerid-anspråks regeln efter installation/uppgradering. Mer information om hur du lägger till issuerid-anspråks regel finns i den här artikeln om [flera domän stöd för federering med Azure AD](how-to-connect-install-multiple-domains.md).
* Port 9090 måste vara öppen utgående för att installationen ska kunna slutföras.

**Nya funktioner:**

* Direktautentisering (för hands version).

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder med hjälp av funktionen för automatisk uppgradering av Azure AD Connect.
## <a name="113430"></a>1.1.343.0
Lanserad: november 2016

**Känt problem:**

* Issuerid-anspråks regeln för AD FS saknas i den här versionen. Issuerid-anspråks regeln krävs om du federerar flera domäner med Azure AD. Om du använder Azure AD Connect för att hantera den lokala AD FS distributionen tar uppgraderingen av den här versionen bort den befintliga issuerid-anspråks regeln från din AD FS konfiguration. Du kan lösa problemet genom att lägga till issuerid-anspråks regeln efter installation/uppgradering. Mer information om hur du lägger till issuerid-anspråks regel finns i den här artikeln om [flera domän stöd för federering med Azure AD](how-to-connect-install-multiple-domains.md).

**Korrigerade problem:**

* Ibland Miss lyckas installationen av Azure AD Connect eftersom det inte går att skapa ett lokalt tjänst konto vars lösen ord uppfyller den komplexitets nivå som anges av organisationens lösen ords princip.
* Ett problem har åtgärd ATS där Join-regler inte utvärderas på nytt när ett objekt i kopplings utrymmet samtidigt blir utanför räckvidden för en kopplings regel och blir inom räckvidden för en annan. Detta kan inträffa om du har två eller fler kopplings regler vars kopplings villkor är ömsesidigt uteslutande.
* Ett problem har åtgärd ATS där regler för inkommande synkronisering (från Azure AD), som inte innehåller kopplings regler, inte bearbetas om de har lägre prioritets värden än de som innehåller kopplings regler.

**Förbättringar:**

* Stöd har lagts till för att installera Azure AD Connect på Windows Server 2016 standard eller högre.
* Stöd har lagts till för att använda SQL Server 2016 som fjärrdatabas för Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Lanserad: augusti 2016

**Korrigerade problem:**

* Ändringar i synkroniseringsstatus sker inte förrän nästa synkronisering har slutförts.
* Azure AD Connects guiden accepterar inte ett Azure AD-konto vars användar namn börjar med ett under streck ( \_ ).
* Azure AD Connects guiden kan inte autentisera Azure AD-kontot om konto lösen ordet innehåller för många specialtecken. Fel meddelandet "Det gick inte att validera autentiseringsuppgifterna. Ett oväntat fel har inträffat. " returneras.
* Om du avinstallerar en fristående server inaktive ras Lösenordssynkronisering i Azure AD-klienten och synkroniseringen av lösen ordet Miss lyckas med den aktiva servern.
* Lösenordssynkronisering Miss lyckas i ovanliga fall när det inte finns någon lösen ords-hash lagrad på användaren.
* När Azure AD Connect Server är aktive rad för mellanlagringsplats inaktive ras inte tillbakaskrivning av lösen ord tillfälligt.
* Guiden för Azure AD Connect visar inte konfigurationen av den faktiska lösen ords synkroniseringen och tillbakaskrivning av lösen ord när servern är i mellanlagrings läge. De visas alltid som inaktiverade.
* Konfigurations ändringar av Lösenordssynkronisering och tillbakaskrivning av lösen ord sparas inte av Azure AD Connect guiden när servern är i mellanlagrings läge.

**Förbättringar:**

* Start-ADSyncSyncCycle-cmdleten har uppdaterats för att ange om den kan starta en ny synkronisering eller inte.
* Stop-ADSyncSyncCycle cmdlet har lagts till för att avsluta synkroniseringen och åtgärden, som pågår för närvarande.
* Den Stop-ADSyncScheduler cmdleten har uppdaterats för att avsluta synkroniseringen och åtgärden, som pågår just nu.
* När du konfigurerar [katalog tillägg](how-to-connect-sync-feature-directory-extensions.md) i guiden Azure AD Connect kan du nu välja Azure AD-attributet av typen "Teletex String".

## <a name="111890"></a>1.1.189.0
Lanserad: 2016 juni

**Korrigerade problem och förbättringar:**

* Azure AD Connect kan nu installeras på en FIPS-kompatibel server.
  * Information om Lösenordssynkronisering finns i [Password hash Sync and FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Ett problem har åtgärd ATS där ett NetBIOS-namn inte kunde matchas mot FQDN i Active Directory-anslutningen.

## <a name="111800"></a>1.1.180.0
Lanserad: maj 2016

**Nya funktioner:**

* Varnar och hjälper dig att verifiera domäner om du inte gjorde det innan du kör Azure AD Connect.
* Stöd har lagts till för [Microsoft Cloud Tyskland](reference-connect-instances.md#microsoft-cloud-germany).
* Stöd har lagts till för den senaste [Microsoft Azure Government moln](reference-connect-instances.md#microsoft-azure-government) infrastrukturen med nya URL-krav.

**Korrigerade problem och förbättringar:**

* Filtrering har lagts till i regel redigeraren för synkronisering för att göra det enkelt att hitta regler för synkronisering.
* Bättre prestanda när du tar bort ett anslutnings utrymme.
* Ett problem har åtgärd ATS när samma objekt hade tagits bort och lagts till i samma körning (kallas ta bort/Lägg till).
* En inaktive rad Synkroniseringsregel aktiverar inte längre inkluderade objekt och attribut vid uppgradering eller katalog schema uppdatering.

## <a name="111300"></a>1.1.130.0
Lanserad: april 2016

**Nya funktioner:**

* Stöd har lagts till för multi-Value-attribut till [katalog tillägg](how-to-connect-sync-feature-directory-extensions.md).
* Stöd har lagts till för att fler konfigurations variationer för [automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) ska anses vara giltiga för uppgradering.
* Några cmdletar har lagts till för den [anpassade Schemaläggaren](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Lanserad: mars 2016

**Korrigerade problem:**

* Det går inte att använda Express installation på Windows Server 2008 (före R2) eftersom Lösenordssynkronisering inte stöds på det här operativ systemet.
* Uppgraderingen från DirSync med en anpassad filter konfiguration fungerade inte som förväntat.
* När du uppgraderar till en nyare version och det inte finns några ändringar i konfigurationen, ska en fullständig import/synkronisering inte schemaläggas.

## <a name="111100"></a>1.1.110.0
Lanserad: februari 2016

**Korrigerade problem:**

* Uppgradering från tidigare versioner fungerar inte om installationen inte finns i standardmappen C:\Program Files.
* Om du installerar och avmarkerar **starta synkroniseringsprocessen** i slutet av installations guiden och kör installations guiden en andra gång aktive ras inte Scheduler.
* Scheduler fungerar inte som förväntat på servrar där formatet US-en datum/tid inte används. Det kommer också att blockeras `Get-ADSyncScheduler` för att returnera korrekta tider.
* Om du har installerat en tidigare version av Azure AD Connect med AD FS som inloggnings alternativ och uppgradering kan du inte köra installations guiden igen.

## <a name="111050"></a>1.1.105.0
Lanserad: februari 2016

**Nya funktioner:**

* Funktion för [automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) för kunder med Express inställningar.
* Stöd för den globala administratören med hjälp av Azure AD Multi-Factor Authentication och Privileged Identity Management i installations guiden.
  * Du måste tillåta att proxyn även tillåter trafik till https://secure.aadcdn.microsoftonline-p.com om du använder Multi-Factor Authentication.
  * Du måste lägga till https://secure.aadcdn.microsoftonline-p.com i listan över betrodda platser för att Multi-Factor Authentication ska fungera korrekt.
* Tillåt ändring av användarens inloggnings metod efter den första installationen.
* Tillåt [domän-och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering) i installations guiden. Detta gör det också möjligt att ansluta till skogar där inte alla domäner är tillgängliga.
* [Scheduler](how-to-connect-sync-feature-scheduler.md) är inbyggd i Synkroniseringsmotorn.

**Funktioner som höjs från för hands version till GA:**

* [Tillbakaskrivning av enhet](how-to-connect-device-writeback.md).
* [Katalog tillägg](how-to-connect-sync-feature-directory-extensions.md).

**Nya för hands versions funktioner:**

* Det nya standard intervallet för synkronisering är 30 minuter. Används till tre timmar för alla tidigare versioner. Lägger till stöd för att ändra [Scheduler](how-to-connect-sync-feature-scheduler.md) -beteendet.

**Korrigerade problem:**

* Sidan verifiera DNS-domäner känner inte alltid igen domänerna.
* Fråga efter autentiseringsuppgifter för domän administratör när du konfigurerar AD FS.
* De lokala AD-kontona kan inte identifieras av installations guiden om de finns i en domän med ett annat DNS-träd än rot domänen.

## <a name="1091310"></a>1.0.9131.0
Lanserad: december 2015

**Korrigerade problem:**

* Lösenordssynkronisering fungerar kanske inte när du ändrar lösen ord i Active Directory Domain Services (AD DS), men fungerar när du anger ett lösen ord.
* När du har en proxyserver kan autentiseringen till Azure AD Miss Miss kunna under installationen, eller om en uppgradering avbryts på konfigurations sidan.
* Uppdatering från en tidigare version av Azure AD Connect med en fullständig SQL Server instans Miss lyckas om du inte är en SQL Server system administratör (SA).
* Om du uppdaterar från en tidigare version av Azure AD Connect med en fjärran sluten SQL Server visas fel meddelandet "Det gick inte att komma åt ADSync SQL-databasen".

## <a name="1091250"></a>1.0.9125.0
Lanserad: november 2015

**Nya funktioner:**

* Kan konfigurera om AD FS till Azure AD-förtroende.
* Kan uppdatera Active Directory-schemat och återskapa regler för synkronisering.
* Kan inaktivera en Synkroniseringsregel.
* Kan definiera "AuthoritativeNull" som en ny litteral i en Synkroniseringsregel.

**Nya för hands versions funktioner:**

* [Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md).
* Stöd för [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) Lösenordssynkronisering.

**Nytt scenario som stöds:**

* Har stöd för flera lokala Exchange-organisationer. Mer information finns i [hybrid distributioner med flera Active Directory-skogar](/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Korrigerade problem:**

* Problem med Lösenordssynkronisering:
  * Ett objekt som har flyttats från utanför räckvidden till omfånget kommer inte att synkroniseras med sitt lösen ord. Detta inkluderar både OU-och attribut filtrering.
  * Om du väljer en ny ORGANISATIONSENHET som ska tas med i synkronisering krävs inte en fullständig synkronisering av lösen ord.
  * När en inaktive rad användare är aktive rad synkroniseras inte lösen ordet.
  * Kön för återförsök för lösen ord är oändlig och den tidigare gränsen på 5 000 objekt som ska dras tillbaka har tagits bort.
* Det går inte att ansluta till Active Directory med Windows Server 2016-skogs funktions nivå.
* Det går inte att ändra gruppen som används för grupp filtrering efter den första installationen.
* Skapar inte längre en ny användar profil på Azure AD Connect servern för varje användare som gör en lösen ords ändring med tillbakaskrivning av lösen ord aktiverat.
* Det går inte att använda långa heltals värden i omfångs regler för synkronisering.
* Kryss rutan "tillbakaskrivning av enhet" förblir inaktive rad om det inte går att komma åt domänkontrollanter.

## <a name="1086670"></a>1.0.8667.0
Lanserad: augusti 2015

**Nya funktioner:**

* Installations guiden för Azure AD Connect är nu lokaliserad till alla Windows Server-språk.
* Stöd har lagts till för att låsa upp kontot när du använder lösen ords hantering i Azure AD.

**Korrigerade problem:**

* Azure AD Connect installations guiden kraschar om en annan användare fortsätter installationen i stället för den person som först startade installationen.
* Om en tidigare avinstallation av Azure AD Connect Miss lyckas med att avinstallera Azure AD Connect synkront, går det inte att installera om.
* Det går inte att installera Azure AD Connect med Express installation om användaren inte finns i skogens rotdomän eller om en icke-engelsk version av Active Directory används.
* Om det fullständiga domän namnet för det Active Directory användar kontot inte kan lösas visas ett missvisande fel meddelande om att det inte gick att bekräfta schemat.
* Om kontot som används på Active Directorys anslutningen ändras utanför guiden, Miss lyckas guiden vid efterföljande körningar.
* Azure AD Connect ibland inte att installera på en domänkontrollant.
* Det går inte att aktivera och inaktivera "mellanlagrings läge" om attribut för tillägg har lagts till.
* Tillbakaskrivning av lösen ord fungerar inte i vissa konfigurationer på grund av ett felaktigt lösen ord på Active Directory anslutningen.
* Det går inte att uppgradera DirSync om ett unikt namn (DN) används i filtrering av attribut.
* Överdriven processor användning vid användning av lösen ords återställning.

**Borttagna för hands versions funktioner:**

* För hands versionen av funktionen [User tillbakaskrivning](how-to-connect-preview.md#user-writeback) togs tillfälligt bort baserat på feedback från våra Preview-kunder. Den kommer att läggas till senare när vi har åtgärdat den feedback som du har fått.

## <a name="1086410"></a>1.0.8641.0
Lanserad: 2015 juni

**Första versionen av Azure AD Connect.**

Namnet har ändrats från Azure AD Sync till Azure AD Connect.

**Nya funktioner:**

* Installation av [Express inställningar](how-to-connect-install-express.md)
* Kan [konfigurera AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Kan [Uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md)
* [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Infört [mellanlagrings läge](how-to-connect-sync-staging-server.md)

**Nya för hands versions funktioner:**

* [Tillbakaskrivning av användare](how-to-connect-preview.md#user-writeback)
* [Tillbakaskrivning av enheter](how-to-connect-device-writeback.md)
* [Katalogtillägg](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Lanserad: maj 2015

**Nytt krav:**

* Azure AD Sync kräver nu att .NET Framework version 4.5.1 ska installeras.

**Korrigerade problem:**

* Tillbakaskrivning av lösen ord från Azure AD fungerar inte med ett Azure Service Bus anslutnings fel.

## <a name="104910413"></a>1.0.491.0413
Lanserad: april 2015

**Korrigerade problem och förbättringar:**

* Active Directory Connector bearbetar inte borttagningarna korrekt om pappers korgen är aktive rad och det finns flera domäner i skogen.
* Prestanda för import åtgärder har förbättrats för den Azure Active Directory anslutningen.
* När en grupp har överskridit medlemskaps gränsen (som standard är gränsen inställd på 50 000 objekt), gruppen har tagits bort i Azure Active Directory. Med det nya beteendet tas inte gruppen bort, ett fel genereras och nya medlemskaps ändringar exporteras inte.
* Det går inte att tillhandahålla ett nytt objekt om en mellanlagrad borttagning med samma DN redan finns i anslutnings utrymmet.
* Vissa objekt har marker ATS för synkronisering under en Delta-synkronisering trots att det inte finns någon ändring som är mellanlagrad på objektet.
* Om du tvingar fram en Lösenordssynkronisering tas även listan över önskade domänkontrollanter bort.
* CSExportAnalyzer har problem med vissa objekt tillstånd.

**Nya funktioner:**

* En anslutning kan nu ansluta till objekt typen "ANY" i MV.

## <a name="104850222"></a>1.0.485.0222
Lanserad: februari 2015

**Förbättringar:**

* Bättre import prestanda.

**Korrigerade problem:**

* Lösenordssynkronisering följer attributet cloudFiltered som används vid filtrering av attribut. Filtrerade objekt omfattas inte längre av Lösenordssynkronisering.
* I sällsynta fall där topologin hade många domänkontrollanter fungerar inte Lösenordssynkronisering.
* "Stoppad-Server" vid import från Azure AD Connector efter att enhets hantering har Aktiver ATS i Azure AD/Intune.
* Att ansluta externa säkerhets objekt (FSPs) från flera domäner i samma skog orsakar ett tvetydigt kopplings fel.

## <a name="104751202"></a>1.0.475.1202
Lanserad: december 2014

**Nya funktioner:**

* Lösenordssynkronisering med attribut-baserad filtrering stöds nu. Mer information finns i Lösenordssynkronisering [med filtrering](how-to-connect-sync-configure-filtering.md).
* Ms-DS-ExternalDirectoryObjectID-attributet skrivs tillbaka till Active Directory. Den här funktionen lägger till stöd för Microsoft 365-program. Den använder OAuth2 för att komma åt online-och lokala post lådor i en hybrid Exchange-distribution.

**Problem med fasta uppgraderingar:**

* Det finns en nyare version av inloggnings assistenten på servern.
* En anpassad installations Sök väg användes för att installera Azure AD Sync.
* Ett ogiltigt anpassat anslutnings villkor blockerar uppgraderingen.

**Andra korrigeringar:**

* Korrigerade mallarna för Office Pro plus.
* Problem med fasta installationer som orsakas av användar namn som börjar med ett bindestreck.
* Åtgärdade inställningen för sourceAnchor när du kör installations guiden en andra gång.
* Fast ETW-spårning för Lösenordssynkronisering.

## <a name="104701023"></a>1.0.470.1023
Lanserad: oktober 2014

**Nya funktioner:**

* Lösenordssynkronisering från flera lokala Active Directory till Azure AD.
* Lokaliserat installations gränssnitt för alla Windows Server-språk.

**Uppgradera från AADSync 1,0 GA**

Om du redan har Azure AD Sync installerat, finns det ytterligare ett steg som du måste vidta om du har ändrat någon av de färdiga reglerna för synkronisering. När du har uppgraderat till 1.0.470.1023-versionen dupliceras de regler för synkronisering som du har ändrat. Gör följande för varje modifierad Synkroniseringsregel:

1. Leta upp den Synkroniseringsregel som du har ändrat och anteckna ändringarna.
1. Ta bort synkroniseringsregeln.
1. Leta upp den nya synkroniseringsregeln som skapats av Azure AD Sync och tillämpa sedan ändringarna igen.

**Behörigheter för Active Directory kontot**

Active Directory kontot måste beviljas ytterligare behörighet för att kunna läsa lösen ordets hash-värden från Active Directory. Behörigheterna för att bevilja kallas "replikera katalog ändringar" och "replikera katalog ändringar". Båda behörigheterna krävs för att kunna läsa lösen ordets hash-värden.

## <a name="104190911"></a>1.0.419.0911
Lanserad: september 2014

**Första versionen av Azure AD Sync.**

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).