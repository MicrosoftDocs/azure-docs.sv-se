---
title: Azure Active Directory Connect vanliga frågor och svar – | Microsoft Docs
description: I den här artikeln får du svar på vanliga frågor om Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fa195fd8d40c6b67a9c446f0b2320fe47d75259
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092674"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect vanliga frågor och svar

## <a name="general-installation"></a>Allmän installation

**F: Hur kan jag skärp min Azure AD Connect-Server för att minska risken för attacker på säkerheten?**

Microsoft rekommenderar att du skärper Azure AD Connect-servern för att minska säkerhets attack ytan för den här viktiga komponenten i din IT-miljö.  Genom att följa rekommendationerna nedan minskar du säkerhets riskerna för din organisation.

* Distribuera Azure AD Connect på en domänansluten Server och begränsa administrativ åtkomst till domän administratörer eller andra tätt kontrollerade säkerhets grupper

Mer information finns i: 

* [Skydda administratörer grupper](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Skydda inbyggda administratörs konton](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Säkerhets förbättring och skydd genom att minska attack ytorna](/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Minska den Active Directory angrepps ytan](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**F: kommer installationen att fungera om den globala administratörs administratören för Azure Active Directory (Azure AD) har 2FA) aktiverat?**  
Från och med februari 2016-versioner stöds det här scenariot.

**F: finns det ett sätt att installera Azure AD Connect obevakad?**  
Azure AD Connect installationen stöds bara när du använder installations guiden. En obevakad, tyst installation stöds inte.

**F: Jag har en skog där en domän inte kan kontaktas. Hur gör jag för att installera Azure AD Connect?**  
Från och med februari 2016-versioner stöds det här scenariot.

**F: fungerar Azure Active Directory Domain Services (Azure AD DS)-hälso agenten på Server Core?**  
Ja. När du har installerat agenten kan du slutföra registrerings processen genom att använda följande PowerShell-cmdlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**F: stöder Azure AD Connect synkronisering från två domäner till en Azure AD?**  
Ja, det här scenariot stöds. Referera till [flera domäner](how-to-connect-install-multiple-domains.md).
 
**F: kan du ha flera kopplingar för samma Active Directory domän i Azure AD Connect?**  
Nej, flera kopplingar för samma AD-domän stöds inte. 

**F: kan jag flytta Azure AD Connect-databasen från den lokala databasen till en fjärran sluten SQL Server instans?**   
Ja, följande steg innehåller allmän vägledning om hur du gör detta. Vi arbetar för närvarande i ett mer detaljerat dokument.
1. Säkerhetskopiera LocalDB ADSync-databasen.
Det enklaste sättet att göra detta är att använda SQL Server Management Studio installerat på samma dator som Azure AD Connect. Anslut till *(LocalDb) .\ADSync* och säkerhetskopiera ADSync-databasen.

2. Återställ ADSync-databasen till fjärrinstansen SQL Server.

3. Installera Azure AD Connect mot den befintliga [fjärranslutna SQL-databasen](how-to-connect-install-existing-database.md).
   Artikeln visar hur du migrerar till att använda en lokal SQL-databas. Om du migrerar till att använda en fjärran sluten SQL-databas i steg 5 i processen måste du också ange ett befintligt tjänst konto som Windows Sync-tjänsten ska köras som. Det här tjänst kontot för synkroniseringstjänsten beskrivs här:
   
      **Använd ett befintligt tjänst konto**: som standard använder Azure AD Connect ett virtuellt tjänst konto för de synkroniseringstjänst som ska användas. Om du använder en fjärran sluten SQL Server instans eller använder en proxy som kräver autentisering, använder du ett hanterat tjänst konto eller ett tjänst konto i domänen och känner till lösen ordet. I detta fall anger du det konto som ska användas. Se till att användare som kör installationen är system administratörer i SQL så att inloggnings uppgifter för tjänst kontot kan skapas. Mer information finns i [Azure AD Connect konton och behörigheter](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Med den senaste versionen kan SQL-administratören nu distribuera databasen ”out of band” och därefter kan den installeras av Azure AD Connect-administratören med databasägarrättigheter. Mer information finns i [installera Azure AD Connect med hjälp av SQL-delegerad administratörs behörighet](how-to-connect-install-sql-delegation.md).

För att det ska vara enkelt, rekommenderar vi att användare som installerar Azure AD Connect är system administratörer i SQL. Med de senaste versionerna kan du nu använda delegerade SQL-administratörer, enligt beskrivningen i [installera Azure AD Connect med hjälp av SQL-delegerad administratörs behörighet](how-to-connect-install-sql-delegation.md).

**F: vilka är några av de bästa metoderna från fältet?**  

Följande är ett informations dokument som visar några av de bästa metoderna som hjälper dig, support och våra konsulter som har utvecklats under åren.  Detta visas i en punkt lista som snabbt kan refereras till.  Även om den här listan försöker vara omfattande, kan det finnas ytterligare metod tips som kanske inte har gjort dem i listan ännu.

- Om du använder fullständig SQL ska det fortfarande vara lokalt vs. Remote
    - Färre hopp
    - Enklare att felsöka
    - Mindre komplexitet
    - Du måste ange resurser för SQL och tillåta omkostnader för Azure AD Connect och OS
- Kringgå proxy om du har möjlighet att kringgå proxyn måste du se till att timeoutvärdet är större än 5 minuter.
- Om proxy krävs måste du lägga till proxyn i machine.config-filen
- Tänk på lokala SQL-jobb och underhåll och hur de påverkar Azure AD Connect – särskilt Omindexering
- Kontrol lera att DNS kan lösa externt
- Kontrol lera att [Server specifikationerna](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) är per rekommendation oavsett om du använder fysiska eller virtuella servrar
- Se till att om du använder en virtuell server som de nödvändiga resurserna är dedikerade
- Kontrol lera att disk-och disk konfigurationen uppfyller metod tipsen för SQL Server
- Installera och konfigurera Azure AD Connect Health för övervakning
- Använd tröskelvärdet för borttagning som är inbyggt i Azure AD Connect.
- Granska uppdateringar noggrant för att förbereda för alla ändringar och nya attribut som kan läggas till
- Säkerhetskopiera allt
    - Säkerhets kopierings nycklar
    - Regler för synkronisering av säkerhets kopiering
    - Konfiguration av säkerhets kopierings Server
    - Säkerhets kopierings SQL Database
- Se till att det inte finns några säkerhets kopierings agenter från tredje part som säkerhetskopierar SQL utan SQL VSS-skrivaren (vanligt i virtuella servrar med ögonblicks bilder från tredje part)
- Begränsa mängden regler för anpassad synkronisering som används när de lägger till komplexitet
- Behandla Azure AD Connect servrar som nivå 0-servrar
- Leery för att ändra regler för synkronisering av moln utan bra förståelse för påverkan och rätt affärs drivande faktorer
- Kontrol lera att rätt URL-adresser och brand Väggs portar är öppna för stöd för Azure AD Connect och Azure AD Connect Health
- Utnyttja det filtrerade attributet Cloud för att felsöka och förhindra objekt i sken objekt
- Se till att du använder Azure AD Connect konfigurations dokument för att få konsekvens mellan servrar med mellanlagrings servern
- Mellanlagrings servrar ska finnas i separata data Center (fysiska platser
- Mellanlagrings servrar är inte avsedda att vara en lösning med hög tillgänglighet, men du kan ha flera mellanlagrings servrar
- Om du introducerar en "fördröjning"-mellanlagrings servrar kan det minska eventuella avbrott i händelse av fel
- Testa och validera alla uppgraderingar på den tillfälliga servern först
- Validera alltid exporter innan du växlar över till mellanlagrings servern.  Utnyttja en fristående server för fullständig import och fullständiga synkroniseringar för att minska påverkan på verksamheten
- Behåll versions konsekvensen mellan Azure AD Connect servrar så mycket som möjligt 

**F: kan jag tillåta att Azure AD Connect skapar Azure AD Connector-kontot på arbets grupps datorn?**
Nej.  Datorn måste vara domänansluten för att du ska kunna tillåta att Azure AD Connect automatiskt skapar Azure AD Connector-kontot.  

## <a name="network"></a>Nätverk
**F: Jag har en brand vägg, en nätverks enhet eller något annat som begränsar den tid som anslutningarna kan vara öppna i nätverket. Vad ska tids gränsen för klient sidan vara när jag använder Azure AD Connect?**  
All nätverks program vara, fysiska enheter eller något annat som begränsar den längsta tid som anslutningar kan vara öppna bör ha ett tröskelvärde på minst fem minuter (300 sekunder) för anslutning mellan den server där Azure AD Connect-klienten är installerad och Azure Active Directory. Den här rekommendationen gäller även för alla tidigare utgivna Microsoft Identitetssynkronisering-verktyg.

**F: stöds SLDs (Single Label Domains)?**  
Vi rekommenderar starkt att du använder den här nätverks konfigurationen ([se artikeln](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), med Azure AD Connect Sync med en enda etikett domän stöds, så länge nätverks konfigurationen för domänen med en domän fungerar som den ska.

**F: finns det stöd för skogar med åtskilda AD-domäner?**  
Nej, Azure AD Connect stöder inte lokala skogar som innehåller åtskilda namn områden.

**F: är "punktavgränsade" NetBIOS-namn stöds?**  
Nej, Azure AD Connect stöder inte lokala skogar eller domäner där NetBIOS-namnet innehåller en punkt (.).

**F: stöds en ren IPv6-miljö?**  
Nej, Azure AD Connect har inte stöd för en ren IPv6-miljö.

**Q:I ha en miljö med flera skogar och nätverket mellan de två skogarna använder NAT (Network Address Translation). Stöds Azure AD Connect mellan dessa två skogar?**</br>
Nej, det finns inte stöd för att använda Azure AD Connect över NAT. 

## <a name="federation"></a>Federation
**F: Vad gör jag om jag får ett e-postmeddelande som ber mig förnya mitt Microsoft 365-certifikat?**  
Information om hur du förnyar certifikatet finns i [Förnya certifikat](how-to-connect-fed-o365-certs.md).

**F: Jag har angett "uppdatera förlitande part" automatiskt för den Microsoft 365 förlitande parten. Måste jag vidta några åtgärder när mitt token signerings certifikat automatiskt rullar?**  
Använd rikt linjerna som beskrivs i artikeln [Förnya certifikat](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Miljö
**F: stöds det att byta namn på servern när Azure AD Connect har installerats?**  
Nej. Om du ändrar Server namnet renderas inte Synkroniseringsmotorn för att ansluta till SQL Database-instansen och tjänsten kan inte starta.

**F: är regler för nästa generations kryptografiska (NGC) som stöds på en FIPS-aktiverad dator?**  
Nej.  De stöds inte.

**C. Om jag inaktiverade en synkroniserad enhet (till exempel: HAADJ) i Azure Portal, varför den aktive ras igen?**<br>
Synkroniserade enheter kan vara skapade eller hanterade lokalt. Om en synkroniserad enhet har Aktiver ATS lokalt kan den aktive ras på nytt i Azure Portal även om tidigare har inaktiverats av en administratör. Om du vill inaktivera en synkroniserad enhet använder du den lokala Active Directory för att inaktivera dator kontot.

**C. Om jag blockerar användar inloggning på Microsoft 365 eller Azure AD-portalen för synkroniserade användare, varför den är avblockerad vid inloggning?**<br>
Synkroniserade användare kan ha skapats eller hanterats lokalt. Om kontot är aktiverat lokalt kan det avblockera inloggnings blocket som har placerats av administratören.

## <a name="identity-data"></a>Identitets data
**F: Varför matchar inte attributet userPrincipalName (UPN) i Azure AD det lokala UPN?**  
Mer information finns i följande artiklar:

* [Användar namn i Microsoft 365, Azure eller Intune matchar inte det lokala UPN eller alternativa inloggnings-ID](https://mskb.pkisolutions.com/kb/2523192)
* [Ändringarna synkroniseras inte med Azure Active Directory Sync-verktyget när du har ändrat UPN för ett användar konto för att använda en annan federerad domän](https://mskb.pkisolutions.com/kb/2669550)

Du kan också konfigurera Azure AD så att Synkroniseringsmotorn kan uppdatera UPN, enligt beskrivningen i [Azure AD Connect Sync service-funktioner](how-to-connect-syncservice-features.md).

**F: stöds det för att mjuka upp en lokal Azure AD-grupp eller ett kontakt objekt med en befintlig Azure AD-grupp eller ett kontakt objekt?**  
Ja, denna mjuka matchning baseras på proxyAddress. Mjuk matchning stöds inte för grupper som inte är e-postaktiverade.

**F: finns det stöd för att manuellt ange attributet ImmutableId för en befintlig Azure AD-grupp eller ett kontakt objekt för hård matchning till en lokal Azure AD-grupp eller ett kontakt objekt?**  
Nej, det finns för närvarande inte stöd för att ange attributet ImmutableId för en befintlig Azure AD-grupp eller ett kontakt objekt till hård matchning.

## <a name="custom-configuration"></a>Anpassad konfiguration
**F: var finns PowerShell-cmdletar för Azure AD Connect dokumenterade?**  
Med undantag för cmdletarna som dokumenteras på den här platsen, stöds inte andra PowerShell-cmdlets i Azure AD Connect för kund användning.

**F: kan jag använda alternativet "server export/Server import" som finns i Synchronization Service Manager för att flytta konfigurationen mellan servrar?**  
Nej. Det här alternativet hämtar inte alla konfigurations inställningar och bör inte användas. Använd i stället guiden för att skapa den grundläggande konfigurationen på den andra servern och Använd redigeraren för Synkroniseringsregel för att generera PowerShell-skript för att flytta en anpassad regel mellan servrar. Mer information finns i flytta [migrering](how-to-upgrade-previous-version.md#swing-migration).

**F: kan lösen ord cachelagras för Azures inloggnings sida och den här cachelagringen kan förhindras eftersom den innehåller ett indatavärde för lösen ord med attributet *Autoavsluta = "false"* ?**  
För närvarande stöds inte att ändra HTML-attributen för fältet **lösen ord** , inklusive autocomplete-taggen. Vi arbetar för närvarande med en funktion som möjliggör anpassad Java Script, vilket gör att du kan lägga till valfritt attribut i fältet **lösen ord** .

**F: Azures inloggnings sida visar användar namn för användare som tidigare har loggat in. Kan det här beteendet stängas av?**  
För närvarande stöds inte att ändra HTML-attributen för fältet för **lösen ords** Indatatyp, inklusive taggen komplettera automatiskt. Vi arbetar för närvarande med en funktion som möjliggör anpassad Java Script, vilket gör att du kan lägga till valfritt attribut i fältet **lösen ord** .

**F: finns det något sätt att förhindra samtidiga sessioner?**  
Nej.

## <a name="auto-upgrade"></a>Automatisk uppgradering

**F: Vad är fördelarna och konsekvenserna med att använda automatisk uppgradering?**  
Vi rekommenderar att alla kunder aktiverar automatisk uppgradering för sin Azure AD Connect-installation. Fördelen är att du alltid får de senaste korrigeringarna, inklusive säkerhets uppdateringar för säkerhets problem som har påträffats i Azure AD Connect. Uppgraderings processen är problem fritt och sker automatiskt så snart en ny version är tillgänglig. Många tusentals Azure AD Connect kunder använder automatisk uppgradering med varje ny version.

Den automatiska uppgraderings processen fastställer alltid först om en installation är kvalificerad för automatisk uppgradering. Om den är kvalificerad utförs uppgraderingen och testas. Processen omfattar även sökning efter anpassade ändringar i regler och vissa miljö faktorer. Om testerna visar att en uppgradering Miss lyckas återställs den tidigare versionen automatiskt.

Beroende på miljöns storlek kan processen ta några timmar. Medan uppgraderingen pågår sker ingen synkronisering mellan Windows Server Active Directory och Azure AD.

**F: Jag fick ett e-postmeddelande som säger att min automatiska uppgradering inte längre fungerar och jag behöver installera en ny version. Varför måste jag göra detta?**  
Förra året släppte vi en version av Azure AD Connect att, under vissa omständigheter, kan ha inaktiverat funktionen för automatisk uppgradering på servern. Vi har åtgärdat problemet i Azure AD Connect version 1.1.750.0. Om du har drabbats av problemet kan du minimera det genom att köra ett PowerShell-skript för att reparera det eller genom att uppgradera manuellt till den senaste versionen av Azure AD Connect. 

Kör PowerShell-skriptet genom att [Ladda ned skriptet](https://aka.ms/repairaadconnect) och köra det på Azure AD Connect-servern i ett administrativt PowerShell-fönster. Om du vill lära dig hur du kör skriptet kan du [Visa den här korta videon](https://aka.ms/repairaadcau).

Om du vill uppgradera manuellt måste du ladda ned och köra den senaste versionen av AADConnect.msi-filen.
 
-  Om din aktuella version är äldre än 1.1.750.0 [hämtar och uppgraderar du till den senaste versionen](https://www.microsoft.com/download/details.aspx?id=47594).
- Om din Azure AD Connect version är 1.1.750.0 eller senare krävs ingen ytterligare åtgärd. Du använder redan den version som innehåller korrigeringen för automatisk uppgradering. 

**F: Jag har fått ett e-postmeddelande som säger att jag uppgraderar till den senaste versionen för att återaktivera automatisk uppgradering. Jag använder version 1.1.654.0. Måste jag uppgradera?**  
Ja, du måste uppgradera till version 1.1.750.0 eller senare för att återaktivera automatisk uppgradering. [Ladda ned och uppgradera till den senaste versionen](https://www.microsoft.com/download/details.aspx?id=47594).

**F: Jag har fått ett e-postmeddelande som säger att jag uppgraderar till den senaste versionen för att återaktivera automatisk uppgradering. Behöver jag fortfarande installera den senaste versionen om jag har använt PowerShell för att aktivera automatisk uppgradering?**  
Ja, du måste fortfarande uppgradera till version 1.1.750.0 eller senare. Aktivering av tjänsten för automatisk uppgradering med PowerShell minimerar inte det automatiska uppgraderings problemet som påträffades i versioner före 1.1.750.0.

**F: Jag vill uppgradera till en nyare version, men jag är inte säker på vem som installerade Azure AD Connect och vi har inte användar namn och lösen ord. Behöver vi detta?**
Du behöver inte känna till det användar namn och lösen ord som ursprungligen användes för att uppgradera Azure AD Connect. Använd valfritt Azure AD-konto som har rollen global administratör.

**F: Hur kan jag ta reda på vilken version av Azure AD Connect jag använder?**  
Du kan kontrol lera vilken version av Azure AD Connect som är installerad på servern genom att gå till kontroll panelen och leta upp den installerade versionen av Microsoft Azure AD Anslut genom att välja **program**  >  **program och funktioner**, som du ser här:

![Azure AD Connect version på kontroll panelen](./media/reference-connect-faq/faq1.png)

**F: Hur gör jag för att uppgradera till den senaste versionen av Azure AD Connect?**  
Information om hur du uppgraderar till den senaste versionen finns [Azure AD Connect: uppgradera från en tidigare version till den senaste](how-to-upgrade-previous-version.md). 

**F: vi har redan uppgraderat till den senaste versionen av Azure AD Connect förra året. Behöver vi uppgradera igen?**  
Azure AD Connect-teamet gör uppdateringar av tjänsten ofta. För att kunna dra nytta av fel korrigeringar och säkerhets uppdateringar och nya funktioner är det viktigt att hålla servern uppdaterad med den senaste versionen. Om du aktiverar automatisk uppgradering uppdateras program versionen automatiskt. För att hitta versions historiken för Azure AD Connect, se [Azure AD Connect: versions historik](reference-connect-version-history.md).

**F: hur lång tid tar det att utföra uppgraderingen, och vad påverkar mina användare?**  
Tiden som krävs för uppgraderingen beror på klientens storlek. För större organisationer kan det vara bäst att utföra uppgraderingen på kvälls-eller helgen. Ingen synkroniseringsuppgift sker under uppgraderingen.

**F: Jag tror att jag har uppgraderat till Azure AD Connect, men Office-portalen nämns fortfarande DirSync. Varför är det här?**  
Office-teamet arbetar för att få uppdateringar av Office-portalen att återspegla det aktuella produkt namnet. Det visar inte vilket Sync-verktyg du använder.

**F: min automatiska uppgraderings status står, "inaktive rad". Varför är den inaktive rad? Ska jag aktivera det?**  
En bugg har introducerats i en tidigare version som under vissa omständigheter lämnar statusen för automatisk uppgradering inställd på "inaktive rad". Det är tekniskt möjligt att aktivera den manuellt, men det skulle krävas flera komplexa steg. Det bästa du kan göra är att installera den senaste versionen av Azure AD Connect.

**F: mitt företag har strikt ändrings hanterings krav och jag vill kontrol lera när det skickas ut. Kan jag kontrol lera när automatisk uppgradering startas?**  
Nej, det finns ingen sådan funktion idag. Funktionen utvärderas för framtida versioner.

**F: får jag ett e-postmeddelande om den automatiska uppgraderingen misslyckades? Hur vet jag att det lyckades?**  
Du får inget meddelande om resultatet av uppgraderingen. Funktionen utvärderas för framtida versioner.

**F: publicerar du en tids linje för när du planerar att push-överföra automatiska uppgraderingar?**  
Automatisk uppgradering är det första steget i versions processen för en nyare version. När det finns en ny version flyttas uppgraderingar automatiskt. Nyare versioner av Azure AD Connect förannonseras i [Azure AD-översikten](../fundamentals/whats-new.md).

**F: uppgraderas även Azure AD Connect Health automatiskt?**  
Ja, automatisk uppgradering uppgraderar också Azure AD Connect Health.

**F: vill du också uppgradera Azure AD Connect-servrar automatiskt i mellanlagrings läge?**  
Ja, du kan uppgradera en Azure AD Connect-Server automatiskt i mellanlagrings läge.

**F: om den automatiska uppgraderingen Miss lyckas och Azure AD Connect servern inte startar, vad ska jag göra?**  
I sällsynta fall startar inte tjänsten Azure AD Connect när du har utfört uppgraderingen. I sådana fall löser det vanligt vis problemet genom att starta om servern. Om tjänsten Azure AD Connect fortfarande inte startar öppnar du ett support ärende. Mer information finns i [skapa en tjänstbegäran att kontakta Microsoft 365 support](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support). 

**F: Jag är inte säker på vad riskerna är när jag uppgraderar till en nyare version av Azure AD Connect. Kan du ringa mig för att hjälpa mig med uppgraderingen?**  
Om du behöver hjälp med att uppgradera till en nyare version av Azure AD Connect öppnar du ett support ärende för [att skapa en tjänstbegäran att kontakta Microsoft 365 support](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support).

## <a name="operational-best-practice"></a>Metod tips för användning    
Nedan följer några metod tips som du bör implementera när du synkroniserar mellan Windows Server Active Directory och Azure Active Directory.

**Använd Multi-Factor Authentication för alla synkroniserade konton** Azure AD Multi-Factor Authentication hjälper till att skydda åtkomsten till data och program samtidigt som användarnas skull bibehålls. Den ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering via en rad enkla att använda autentiseringsmetoder. Användare kan kanske inte anropas för MFA baserat på konfigurations beslut som en administratör gör. Du kan läsa mer om MFA här: https://www.microsoft.com/security/business/identity/mfa?rtc=1

**Följ säkerhets rikt linjerna för Azure AD Connect Server** Azure AD Connect servern innehåller kritiska identitets data och bör behandlas som en komponent på nivå 0 som dokumenteras i den [Active Directory administrativa nivå modellen](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material). Läs även våra [rikt linjer för att skydda din AADConnect-Server](./how-to-connect-install-prerequisites.md#azure-ad-connect-server).

**Aktivera PHS för identifiering av läckta autentiseringsuppgifter** Hash-synkronisering av lösen ord möjliggör även [läcka autentiseringsuppgifter](../identity-protection/concept-identity-protection-risks.md) för ditt hybrid konto. Microsoft arbetar tillsammans med mörka webb forskare och juridiska myndigheter för att hitta offentligt tillgängliga användar namn/lösen ord. Om något av dessa par matchar användarens, flyttas det associerade kontot till hög risk. 


## <a name="troubleshooting"></a>Felsökning
**F: Hur kan jag få hjälp med Azure AD Connect?**

[Sök i Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Sök i KB efter tekniska lösningar på vanliga problem med att avbryta frågor om stöd för Azure AD Connect.

[Sidan Microsoft Q&en fråga för Azure Active Directory](/answers/topics/azure-active-directory.html)

* Sök efter tekniska frågor och svar eller Ställ egna frågor genom att gå till [Azure AD-communityn](/answers/topics/azure-active-directory.html).

[Få support för Azure AD](../fundamentals/active-directory-troubleshooting-support-howto.md)

**F: Varför ser jag händelserna 6311 och 6401 uppstår efter synkroniseringsfel?**

Händelserna 6311- **servern påträffade ett oväntat fel vid ett återanrop** och 6401- **hanterings agent styrenheten påträffade ett oväntat fel** . loggas alltid efter ett synkroniseringsfel-fel. För att lösa dessa fel måste du rensa stegen i synkroniseringen.  Mer information finns i [fel söknings fel under synkronisering](tshoot-connect-sync-errors.md) och [Felsöka synkronisering av objekt med Azure AD Connect Sync](tshoot-connect-objectsync.md)