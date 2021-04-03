---
title: Anpassa en installation av Azure Active Directory Connect
description: I den här artikeln beskrivs anpassade installations alternativ för Azure AD Connect. Följ dessa instruktioner om du ska installera Active Directory via Azure AD Connect.
services: active-directory
keywords: vad är Azure AD Connect, installera Active Directory, komponenter som krävs för Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096359"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Anpassad installation av Azure Active Directory Connect
Använd *anpassade inställningar* i Azure Active Directory (Azure AD) Connect när du vill ha fler alternativ för installationen. Använd de här inställningarna, till exempel om du har flera skogar eller om du vill konfigurera valfria funktioner. Använd anpassade inställningar i alla fall där [Express installation](how-to-connect-install-express.md) inte uppfyller dina krav på distribution eller topologi.

Krav:
- [Ladda ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Slutför de nödvändiga stegen i [Azure AD Connect: maskin vara och förutsättningar](how-to-connect-install-prerequisites.md). 
- Se till att du har de konton som beskrivs i [Azure AD Connect konton och behörigheter](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Anpassade installations inställningar 

Om du vill konfigurera en anpassad installation för Azure AD Connect går du igenom guide sidorna som beskrivs i följande avsnitt.

### <a name="express-settings"></a>Standardinställningar
På sidan **Express inställningar** väljer du **Anpassa** för att starta en installation med anpassade inställningar.  Resten av den här artikeln vägleder dig genom den anpassade installations processen. Använd följande länkar för att snabbt gå till informationen för en viss sida:

- [Nödvändiga komponenter](#install-required-components)
- [Användar inloggning](#user-sign-in)
- [Anslut till Azure AD](#connect-to-azure-ad)
- [Synkronisera](#sync-pages)

### <a name="install-required-components"></a>Installera nödvändiga komponenter
När du installerar Synchronization Services kan du lämna det valfria konfigurations avsnittet omarkerat. Azure AD Connect konfigurerar allt automatiskt. Den konfigurerar en SQL Server 2012 Express LocalDB-instans, skapar lämpliga grupper och tilldelar behörigheter. Om du vill ändra standardvärdena avmarkerar du lämpliga rutor.  I följande tabell sammanfattas dessa alternativ och det finns länkar till ytterligare information. 

![Skärm bild som visar valfria alternativ för de installations komponenter som krävs i Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Valfri konfiguration | Beskrivning |
| --- | --- |
|Ange en anpassad installations plats| Gör att du kan ändra standard installations Sök vägen för Azure AD Connect.|
| Använda en befintlig SQL-server |Gör att du kan ange SQL Server namn och instans namn. Välj det här alternativet om du redan har en databas server som du vill använda. Vid **instans namn** anger du namnet på instansen, ett kommatecken och port numret om SQL Server instansen inte har aktiverat bläddring.  Ange sedan namnet på den Azure AD Connect databasen.  SQL-privilegierna avgör om en ny databas kan skapas eller om SQL-administratören måste skapa databasen i förväg.  Om du har SQL Server administratörs behörighet (SA), se [installera Azure AD Connect med hjälp av en befintlig databas](how-to-connect-install-existing-database.md).  Om du har delegerad behörighet (DBO), se [installera Azure AD Connect med hjälp av SQL-delegerad administratörs behörighet](how-to-connect-install-sql-delegation.md). |
| Använda ett befintligt tjänstkonto |Som standard tillhandahåller Azure AD Connect ett virtuellt tjänst konto för synkroniseringstjänsten. Om du använder en fjärrinstans av SQL Server eller använder en proxy som kräver autentisering, kan du använda ett *hanterat tjänst konto* eller ett lösenordsskyddat tjänst konto i domänen. I dessa fall anger du det konto som du vill använda. Om du vill köra installationen måste du vara en SA i SQL så att du kan skapa inloggnings uppgifter för tjänst kontot. Mer information finns i [Azure AD Connect konton och behörigheter](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Med den senaste versionen kan SQL-administratören nu etablera databasen out-of-band. Sedan kan Azure AD Connect-administratören installera den med databas ägar rättigheter.  Mer information finns i [installera Azure AD Connect med hjälp av SQL-delegerad administratörs behörighet](how-to-connect-install-sql-delegation.md).|
| Ange anpassade synkroniseringsgrupper |När Sync-tjänsterna är installerade skapar Azure AD Connect fyra grupper som är lokala för-servern som standard. De här grupperna är administratörer, operatörer, bläddra och lösen ords återställning. Du kan ange dina egna grupper här. Grupperna måste vara lokala på servern. De kan inte hittas i domänen. |
|Importera synkroniseringsinställningar (för hands version)|Gör att du kan importera inställningar från andra versioner av Azure AD Connect.  Mer information finns i [Importera och exportera Azure AD Connect konfigurations inställningar](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Användarinloggning
När du har installerat de nödvändiga komponenterna väljer du användarnas metod för enkel inloggning. Följande tabell beskriver kortfattat de tillgängliga alternativen. En fullständig beskrivning av inloggningsmetoderna finns i [Användarinloggning](plan-connect-user-signin.md).

![Skärm bild som visar sidan "användar inloggning". Alternativet "lösen ords-hash-synkronisering" är markerat.](./media/how-to-connect-install-custom/usersignin4.png)

| Alternativ för enkel inloggning | Beskrivning |
| --- | --- |
| Synkronisering av lösenordshash |Användare kan logga in på Microsofts moln tjänster, till exempel Microsoft 365, genom att använda samma lösen ord som de använder i sitt lokala nätverk. Användar lösen ord synkroniseras till Azure AD som lösen ords-hash. Autentisering sker i molnet. Mer information finns i [synkronisering av lösen ord för hash](how-to-connect-password-hash-synchronization.md). |
|Direktautentisering|Användare kan logga in på Microsofts moln tjänster, till exempel Microsoft 365, genom att använda samma lösen ord som de använder i sitt lokala nätverk.  Användar lösen ord verifieras genom att skickas till den lokala Active Directory domänkontrollanten.
| Federation med AD FS |Användare kan logga in på Microsofts moln tjänster, till exempel Microsoft 365, genom att använda samma lösen ord som de använder i sitt lokala nätverk.  Användare omdirigeras till sina lokala Azure Directory Federation Services-instanser (AD FS) för att logga in. Autentisering sker lokalt. |
| Federation med PingFederate|Användare kan logga in på Microsofts moln tjänster, till exempel Microsoft 365, genom att använda samma lösen ord som de använder i sitt lokala nätverk.  Användare omdirigeras till den lokala PingFederate-instansen för att logga in. Autentisering sker lokalt. |
| Konfigurera inte |Ingen användar inloggnings funktion installeras eller konfigureras. Välj det här alternativet om du redan har en Federations Server från tredje part eller en annan lösning på plats. |
|Aktivera enkel inloggning|Det här alternativet är tillgängligt med både hash-synkronisering av lösen ord och direktautentisering. Det ger en enkel inloggnings upplevelse för Skriv bords användare i företags nätverk. Mer information finns i [enkel inloggning](how-to-connect-sso.md). </br></br>**Obs:** För AD FS kunder är det här alternativet inte tillgängligt. AD FS har redan samma nivå av enkel inloggning.</br>

### <a name="connect-to-azure-ad"></a>Anslut till Azure AD
På sidan **Anslut till Azure AD** anger du ett globalt administratörs konto och lösen ord. Om du har valt **Federation med AD FS** på föregående sida loggar du inte in med ett konto som finns i en domän som du planerar att aktivera för federation. 

Du kanske vill använda ett konto i standard domänen *onmicrosoft.com* , som medföljer Azure AD-klienten. Det här kontot används endast för att skapa ett tjänst konto i Azure AD. Den används inte när installationen är klar.
  
![Skärm bild som visar sidan Anslut till Azure AD.](./media/how-to-connect-install-custom/connectaad.png)

Om ditt globala administratörs konto har multifaktorautentisering aktiverat, anger du lösen ordet igen i inloggnings fönstret och du måste slutföra multifaktorautentisering för multifaktorautentisering. Utmaningen kan vara en verifierings kod eller ett telefonsamtal.  

![Skärm bild som visar sidan Anslut till Azure AD. Ett fält för multifaktorautentisering uppmanas användaren att ange en kod.](./media/how-to-connect-install-custom/connectaadmfa.png)

Det globala administratörs kontot kan också ha [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) aktiverat.

Om du ser ett fel eller har problem med anslutningen kan du läsa [Felsök anslutnings problem](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Synkronisera sidor

I följande avsnitt beskrivs sidorna i **Sync** -avsnittet.

### <a name="connect-your-directories"></a>Anslut dina kataloger
För att ansluta till Active Directory Domain Services (Azure AD DS) behöver Azure AD Connect skogens namn och autentiseringsuppgifter för ett konto som har tillräcklig behörighet.

![Skärm bild som visar sidan "Anslut dina kataloger".](./media/how-to-connect-install-custom/connectdir01.png)

När du har angett skogs namnet och väljer  **Lägg till katalog** visas ett fönster. I följande tabell beskrivs dina alternativ.

| Alternativ | Beskrivning |
| --- | --- |
| Skapa ett nytt konto | Skapa det Azure AD DS-konto som Azure AD Connect måste ansluta till Active Directorys skogen vid katalog synkroniseringen. När du har valt det här alternativet anger du användar namn och lösen ord för ett företags administratörs konto.  Azure AD Connect använder det tillhandahållna företags administratörs kontot för att skapa det nödvändiga Azure AD DS-kontot. Du kan ange domän delen i antingen NetBIOS-format eller FQDN-format. Det vill säga Skriv *FABRIKAM\administrator* eller *FABRIKAM. com\administrator*. |
| Använda befintligt konto | Ange ett befintligt Azure AD DS-konto som Azure AD Connect kan använda för att ansluta till Active Directory-skogen vid katalog synkronisering. Du kan ange domän delen i antingen NetBIOS-format eller FQDN-format. Det vill säga Skriv *FABRIKAM\syncuser* eller *FABRIKAM. com\syncuser*. Det här kontot kan vara ett vanligt användar konto eftersom det bara behöver Läs behörighet som standard. Men beroende på ditt scenario kan du behöva fler behörigheter. Mer information finns i [Azure AD Connect konton och behörigheter](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Skärm bild som visar sidan "Anslut katalog" och fönstret ett D-skogs konto där du kan välja att skapa ett nytt konto eller använda ett befintligt konto.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> Från och med build 1.4.18.0 kan du inte använda ett företags administratörs konto eller ett domän administratörs konto som Azure AD DS-konto. Om du försöker ange ett företags administratörs konto eller ett domän administratörs **konto visas följande** fel meddelande: "det är inte tillåtet att använda ett företags-eller domän administratörs konto för ditt Ad-skogs konto. Låt Azure AD Connect skapa kontot åt dig eller ange ett konto för synkronisering med rätt behörigheter. "
>

### <a name="azure-ad-sign-in-configuration"></a>Inloggningskonfiguration för Azure AD
På sidan **konfiguration av Azure AD-inloggning** granskar du User Principal Name-domäner (UPN) i lokala Azure AD DS. Dessa UPN-domäner har verifierats i Azure AD. På den här sidan konfigurerar du det attribut som ska användas för userPrincipalName.

![Skärm bild som visar overifierade domäner på sidan "konfiguration av Azure A D-inloggning".](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Granska varje domän som marker ATS som **ej tillagd** eller **inte verifierad**. Kontrol lera att de domäner du använder har verifierats i Azure AD. När du har verifierat dina domäner väljer du ikonen cirkulär uppdatering. Mer information finns i [lägga till och verifiera domänen](../fundamentals/add-custom-domain.md).

Användarna använder attributet *userPrincipalName* när de loggar in på Azure AD och Microsoft 365. Azure AD bör verifiera domänerna, även kallade UPN-suffix, innan användarna synkroniseras. Microsoft rekommenderar att du behåller standardattributet userPrincipalName. 

Om userPrincipalName-attributet är nonroutable och inte kan verifieras kan du välja ett annat attribut. Du kan till exempel välja e-post som attributet som innehåller inloggnings-ID: t. När du använder ett annat attribut än userPrincipalName kallas det ett *alternativ-ID*. 

Attributvärdet för alternativ-ID måste följa RFC 822-standarden. Du kan använda ett alternativt ID med hash-synkronisering av lösen ord, direktautentisering och Federation. I Active Directory kan attributet inte definieras som flervärdesattribut, även om det bara har ett enda värde. Mer information om alternativt ID finns i [direktautentisering: vanliga frågor och svar](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname).

>[!NOTE]
> När du aktiverar direktautentisering måste du ha minst en verifierad domän för att kunna fortsätta med den anpassade installations processen.

> [!WARNING]
> Alternativa ID: n är inte kompatibla med alla Microsoft 365 arbets belastningar. Mer information finns i [Konfigurera alternativa inloggnings-ID: n](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Domän- och organisationsenhetsfiltrering
Som standard synkroniseras alla domäner och organisationsenheter (OU). Om du inte vill synkronisera vissa domäner eller organisationsenheter till Azure AD kan du avmarkera lämpliga alternativ.  

![Skärm bild som visar sidan domän och O U-filtrering.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Den här sidan konfigurerar domän-och OU-baserad filtrering. Om du planerar att göra ändringar kan du se [domänbaserad filtrering](how-to-connect-sync-configure-filtering.md#domain-based-filtering) och [OU-baserad filtrering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Vissa organisationsenheter är viktiga för funktioner, så du bör låta dem vara markerade.

Om du använder OU-baserad filtrering med en Azure AD Connect version som är äldre än 1.1.524.0, synkroniseras nya organisationsenheter som standard. Om du inte vill att nya organisationsenheter ska synkroniseras kan du justera standard beteendet efter det [OU-baserade filtrerings](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) steget. För Azure AD Connect 1.1.524.0 eller senare kan du ange om du vill att nya organisationsenheter ska synkroniseras.

Om du planerar att använda [gruppbaserad filtrering](#sync-filtering-based-on-groups)kontrollerar du att organisationsenheten med gruppen ingår och inte är filtrerad med hjälp av ou-filtrering. ORGANISATIONSENHETs filtrering utvärderas innan gruppbaserad filtrering utvärderas.

Det är också möjligt att vissa domäner inte kan kommas åt på grund av brand Väggs begränsningar. Dessa domäner är omarkerade som standard och de visar en varning.  

![Skärm bild som visar oåtkomliga domäner.](./media/how-to-connect-install-custom/unreachable.png)  

Om du ser den här varningen ser du till att dessa domäner faktiskt inte kan kommas åt och att varningen förväntas.

### <a name="uniquely-identifying-your-users"></a>Identifiera användarna unikt

På sidan **identifiera användare** väljer du hur du vill identifiera användare i dina lokala kataloger och hur du identifierar dem med hjälp av attributet sourceAnchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Välj hur användare ska identifieras i dina lokala kataloger
Genom att använda funktionen *matchande över skogar* kan du definiera hur användare från Azure AD DS-skogar representeras i Azure AD. En användare kan bara visas en gång i alla skogar eller ha en kombination av aktiverade och inaktiverade konton. Användaren kan också vara representerad som en kontakt i vissa skogar.

![Skärm bild som visar sidan där du kan identifiera dina användare unikt.](./media/how-to-connect-install-custom/unique2.png)

| Inställning | Beskrivning |
| --- | --- |
| [Användare representeras bara en gång i alla skogar](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Alla användare skapas som enskilda objekt i Azure AD. Objekten är inte anslutna i metaversum. |
| [E-postattribut](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Det här alternativet kopplar ihop användare och kontakter om e-postattributet har samma värde i olika skogar. Använd det här alternativet när dina kontakter har skapats med hjälp av GALSync. Om du väljer det här alternativet synkroniseras inte användar objekt vars e-postattribut är ifyllda till Azure AD. |
| [Attributen ObjectSID och msExchangeMasterAccountSID/msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Det här alternativet kopplar ihop en aktiverad användare i en kontoskog med en inaktiverad användare i en resursskog. I Exchange kallas den här konfigurationen för en länkad postlåda. Du kan använda det här alternativet om du endast använder Lync och om Exchange inte finns i resurs skogen. |
| Attributen SAMAccountName och smek namn |Det här alternativet ansluter till attribut där inloggnings-ID: t för användaren förväntas hittas. |
| Välj ett speciellt attribut |Med det här alternativet kan du välja ett eget attribut. Om du väljer det här alternativet synkroniseras inte användar objekt vars (valda) attribut inte har fyllts i till Azure AD. **Begränsning:** Endast attribut som redan finns i metaversum är tillgängliga för det här alternativet. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Välj hur användare ska identifieras med hjälp av en käll ankare
Attributet *sourceAnchor* är oföränderligt under ett användar objekts livs längd. Det är den primära nyckeln som länkar den lokala användaren till användaren i Azure AD.

| Inställning | Beskrivning |
| --- | --- |
| Låt Azure hantera käll fäst punkten | Välj det här alternativet om du vill att Azure AD ska hämta attributet. Om du väljer det här alternativet tillämpar Azure AD Connect sourceAnchor för val av attribut som beskrivs i [använda MS-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). När den anpassade installationen är klar ser du vilket attribut som har plockats som attributet sourceAnchor. |
| Välj ett speciellt attribut | Välj det här alternativet om du vill ange ett befintligt AD-attribut som attributet sourceAnchor. |

Eftersom attributet sourceAnchor inte kan ändras måste du välja ett lämpligt attribut. En bra kandidat är objectGUID. Det här attributet ändras inte om inte användar kontot flyttas mellan skogar eller domäner. Välj inte attribut som kan ändras när en person Marries eller ändrar tilldelningar. 

Du kan inte använda attribut som innehåller ett @-tecken, så du kan inte använda e-post och userPrincipalName. Attributet är också Skift läges känsligt, så när du flyttar ett objekt mellan skogar bör du se till att bevara versaler och gemener. Binära attribut är base64-kodade, men andra attributtyper förblir i inkodat tillstånd. 

I Federations scenarier och vissa Azure AD-gränssnitt kallas attributet sourceAnchor även för *immutableID*. 

Mer information om käll ankare finns i [design begrepp](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Synkroniseringsfiltrering baserat på grupper
Med funktionen Filtrera – grupper kan du bara synkronisera en liten delmängd av objekt för en pilot. Om du vill använda den här funktionen skapar du en grupp för detta ändamål i din lokala instans av Active Directory. Lägg sedan till användare och grupper som ska synkroniseras till Azure AD som direkta medlemmar. Du kan senare lägga till användare eller ta bort användare från den här gruppen för att underhålla listan med objekt som ska finnas i Azure AD. 

Alla objekt som du vill synkronisera måste vara direkt medlemmar i gruppen. Användare, grupper, kontakter och datorer eller enheter måste alla vara direkta medlemmar. Det kapslade grupp medlemskapet har inte lösts. När du lägger till en grupp som medlem läggs bara själva gruppen till. Dess medlemmar har inte lagts till.

![Skärm bild som visar sidan där du kan välja hur du vill filtrera användare och enheter.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Den här funktionen är avsedd att endast stödja en pilot distribution. Använd den inte i en fullständig produktions distribution.
>

I en fullständig produktions distribution skulle det vara svårt att underhålla en enda grupp och alla dess objekt att synkronisera. Använd en av de metoder som beskrivs i [Konfigurera filtrering](how-to-connect-sync-configure-filtering.md)i stället för funktionen Filtrera-på-grupper.

### <a name="optional-features"></a>Valfria funktioner
På nästa sida kan du välja valfria funktioner för ditt scenario.

>[!WARNING]
>Azure AD Connect versioner 1.0.8641.0 och tidigare förlitar sig på Azure Access Control Service för tillbakaskrivning av lösen ord.  Tjänsten drogs tillbaka den 7 november 2018.  Om du använder någon av dessa versioner av Azure AD Connect och har aktiverat tillbakaskrivning av lösen ord kan användare förlora möjligheten att ändra eller återställa sina lösen ord när tjänsten har dragits tillbaka. Dessa versioner av Azure AD Connect har inte stöd för tillbakaskrivning av lösen ord.
>
>Mer information finns i [Migrera från Azure Access Control Service](../azuread-dev/active-directory-acs-migration.md).
>
>Om du vill använda tillbakaskrivning av lösen ord kan du ladda ned den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Skärm bild som visar sidan "valfria funktioner".](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Om Azure AD Sync eller direkt synkronisering (DirSync) är aktiva aktiverar du inte några tillbakaskrivning i Azure AD Connect.



| Valfria funktioner | Beskrivning |
| --- | --- |
| Exchange hybrid distribution |Funktionen Exchange hybrid distribution möjliggör samexistens av Exchange-postlådor både lokalt och i Microsoft 365. Azure AD Connect synkroniserar en särskild uppsättning [attribut](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) från Azure AD tillbaka till din lokala katalog. |
| Delade Exchange Mail-mappar | Med funktionen gemensamma mappar för Exchange-e-post kan du synkronisera e-postaktiverade objekt i offentliga mappar från din lokala instans av Active Directory till Azure AD. |
| Filtrering av Azure AD-appar och -attribut |Genom att aktivera filtrering av Azure AD-appar och-attribut kan du skräddarsy uppsättningen med synkroniserade attribut. Det här alternativet lägger till ytterligare två konfigurationssidor i guiden. Mer information finns i [Filtrering av Azure AD-appar och -attribut](#azure-ad-app-and-attribute-filtering). |
| Synkronisering av lösenordshash |Om du har valt Federation som inloggnings lösning kan du aktivera synkronisering av lösen ord för hash. Sedan kan du använda det som ett alternativ för säkerhets kopiering.  </br></br>Om du har valt direktautentisering, kan du aktivera det här alternativet för att säkerställa stöd för äldre klienter och för att tillhandahålla en säkerhets kopia.</br></br> Mer information finns i [synkronisering av lösen ord för hash](how-to-connect-password-hash-synchronization.md).|
| Tillbakaskrivning av lösenord |Använd det här alternativet för att se till att lösen ords ändringar som kommer från Azure AD skrivs tillbaka till din lokala katalog. Mer information finns i [Komma igång med lösenordshantering](../authentication/tutorial-enable-sspr.md). |
| Tillbakaskrivning av grupp |Om du använder Microsoft 365 grupper kan du representera grupper i din lokala instans av Active Directory. Det här alternativet är bara tillgängligt om du har Exchange i din lokala instans av Active Directory. Mer information finns i [tillbakaskrivning av Azure AD Connect grupp](how-to-connect-group-writeback.md).|
| Tillbakaskrivning av enheter |För villkorliga åtkomst scenarier använder du det här alternativet för att skriva tillbaka enhets objekt i Azure AD till din lokala instans av Active Directory. Mer information finns i [Aktivera tillbakaskrivning av enheter i Azure AD Connect](how-to-connect-device-writeback.md). |
| Synkronisering av katalogtilläggsattribut |Välj det här alternativet om du vill synkronisera angivna attribut till Azure AD. Mer information finns i [Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Filtrering av Azure AD-appar och -attribut
Om du vill begränsa vilka attribut som synkroniseras till Azure AD börjar du med att välja de tjänster som du använder. Om du ändrar valen på den här sidan måste du uttryckligen välja en ny tjänst genom att köra installations guiden igen.

![Skärm bild som visar valfria funktioner i Azure A D Apps.](./media/how-to-connect-install-custom/azureadapps2.png)

Baserat på de tjänster som du valde i föregående steg visar den här sidan alla attribut som är synkroniserade. Den här listan är en kombination av alla objekt typer som synkroniseras. Om du behöver några attribut för att förbli osynkroniserade kan du ta bort markeringen från dessa attribut.

![Skärm bild som visar valfria funktioner i Azure A D-attribut.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Att ta bort attribut kan påverka funktionaliteten. För metod tips och rekommendationer, se [attribut som ska synkroniseras](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Synkronisering av katalogtilläggsattribut
Du kan utöka schemat i Azure AD med hjälp av anpassade attribut som organisationen lagt till eller med hjälp av andra attribut i Active Directory. Om du vill använda den här funktionen väljer du **katalog tilläggets attribut** på sidan **valfria funktioner** . På sidan **katalog tillägg** kan du välja fler attribut som ska synkroniseras.

>[!NOTE]
>Fältet **tillgängliga attribut** är Skift läges känsligt.

![Skärm bild som visar sidan "katalog tillägg".](./media/how-to-connect-install-custom/extension2.png)

Mer information finns i [Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Aktivera enkel inloggning
På sidan för **enkel inloggning** konfigurerar du enkel inloggning för användning med Lösenordssynkronisering eller direktautentisering. Du gör det här steget en gång för varje skog som synkroniseras med Azure AD. Konfigurationen består av två steg:

1.  Skapa det nödvändiga dator kontot i din lokala instans av Active Directory.
2.  Konfigurera zonen Intranät på klient datorerna så att de stöder enkel inloggning.

#### <a name="create-the-computer-account-in-active-directory"></a>Skapa datorkontot i Active Directory
För varje skog som har lagts till i Azure AD Connect måste du ange autentiseringsuppgifter för domän administratören så att dator kontot kan skapas i varje skog. Autentiseringsuppgifterna används bara för att skapa kontot. De lagras eller används inte för någon annan åtgärd. Lägg till autentiseringsuppgifterna på sidan **aktivera enkel inloggning** , som i följande bild visas.

![Skärm bild som visar sidan "aktivera enkel inloggning". Autentiseringsuppgifter för skogen har lagts till.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Du kan hoppa över skogar där du inte vill använda enkel inloggning.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Konfigurera zonen Intranät för klient datorer
Säkerställ att klienten loggar in automatiskt i zonen Intranät genom att kontrol lera att URL: en är en del av zonen Intranät. Det här steget säkerställer att den domänanslutna datorn automatiskt skickar en Kerberos-biljett till Azure AD när den är ansluten till företags nätverket.

På en dator som har grupprincip hanterings verktyg:

1.  Öppna grupprincip hanterings verktyg.
2.  Redigera grup principen som ska tillämpas på alla användare. Till exempel standard domän principen.
3.  Gå till **användar konfiguration**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Internet Explorer Internet Explorer** på  >  säkerhets sidan för **Internet kontroll panelen**  >  . Välj sedan **plats till zon tilldelnings lista**.
4.  Aktivera principen. I dialog rutan anger du sedan värde namnet `https://autologon.microsoftazuread-sso.com` och värdet `1` . Installations programmet bör se ut som på följande bild.
  
    ![Skärm bild som visar intranäts zoner.](./media/how-to-connect-install-custom/sitezone.png)

6.  Välj **OK** två gånger.

## <a name="configuring-federation-with-ad-fs"></a>Konfigurera federation med AD FS
Du kan konfigurera AD FS med Azure AD Connect på bara några få klick. Innan du börjar behöver du:

* Windows Server 2012 R2 eller senare för Federations servern. Fjärrhantering måste vara aktiverat.
* Windows Server 2012 R2 eller senare för Web Application Proxy-servern. Fjärrhantering måste vara aktiverat.
* Ett TLS/SSL-certifikat för det Federations tjänst namn som du vill använda (till exempel sts.contoso.com).

>[!NOTE]
>Du kan uppdatera ett TLS/SSL-certifikat för AD FS server gruppen genom att använda Azure AD Connect även om du inte använder den för att hantera Federations förtroendet.

### <a name="ad-fs-configuration-prerequisites"></a>Krav för AD FS konfiguration
Kontrol lera att WinRM är aktiverat på fjärrservrarna om du vill konfigurera din AD FS server grupp med hjälp av Azure AD Connect. Se till att du har slutfört de andra uppgifterna i [Federations kraven](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Kontrol lera också att du följer de Port krav som anges i tabellen [Azure AD Connect och Federation/WAP-servrar](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) .

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Skapa en ny AD FS-servergrupp eller använd en befintlig AD FS-servergrupp
Du kan använda en befintlig AD FS server grupp eller skapa en ny. Om du väljer att skapa en ny måste du ange TLS/SSL-certifikatet. Om TLS/SSL-certifikatet skyddas av ett lösen ord uppmanas du att ange lösen ordet.

![Skärm bild som visar sidan "en D F S-grupp"](./media/how-to-connect-install-custom/adfs1.png)

Om du väljer att använda en befintlig AD FS server grupp visas sidan där du kan konfigurera förtroende relationen mellan AD FS och Azure AD.

>[!NOTE]
>Du kan använda Azure AD Connect för att bara hantera en AD FS server grupp. Om du har ett befintligt Federations förtroende där Azure AD har kon figurer ATS i den valda AD FS-servergruppen, Azure AD Connect skapa om förtroendet från grunden.

### <a name="specify-the-ad-fs-servers"></a>Ange AD FS-servrarna
Ange de servrar där du vill installera AD FS. Du kan lägga till en eller flera servrar, beroende på dina kapacitets behov. Innan du konfigurerar den här konfigurationen måste du ansluta alla AD FS servrar till Active Directory. Det här steget krävs inte för Web Application Proxy-servrar. 

Microsoft rekommenderar att du installerar en enskild AD FS-server för test- och pilotdistributioner. Efter den inledande konfigurationen kan du lägga till och distribuera fler servrar för att uppfylla dina skalnings behov genom att köra Azure AD Connect igen.

> [!NOTE]
> Innan du konfigurerar den här konfigurationen måste du kontrol lera att alla dina servrar är anslutna till en Azure AD-domän.
>


![Skärm bild som visar sidan "Federations Servrar".](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Ange webbprogramproxyservrarna
Ange dina Web Application Proxy-servrar. Webbprogramproxy är distribuerat i perimeternätverket, vilket riktar sig mot extra nätet. Det stöder autentiseringsbegäranden från extra nätet. Du kan lägga till en eller flera servrar, beroende på dina kapacitets behov. 

Microsoft rekommenderar att du installerar en enda Webbprogramproxy för test-och pilot distributioner. Efter den inledande konfigurationen kan du lägga till och distribuera fler servrar för att uppfylla dina skalnings behov genom att köra Azure AD Connect igen. Vi rekommenderar att du har ett motsvarande antal proxyservrar för att tillfredsställa autentiseringen från intranätet.

> [!NOTE]
> - Om kontot som du använder inte är en lokal administratör på Webbprogramproxy, uppmanas du att ange administratörsautentiseringsuppgifter.
> - Innan du anger Web Application Proxy-servrar måste du se till att det finns HTTP/HTTPS-anslutning mellan Azure AD Connect-servern och Web Application Proxy-servern.
> - Se till att det finns HTTP/HTTPS-anslutning mellan webb program servern och AD FS servern för att tillåta autentiseringsbegäranden att flöda genom.
>


![Skärm bild som visar sidan Web Application Proxy-servrar.](./media/how-to-connect-install-custom/adfs3.png)

Du uppmanas att ange autentiseringsuppgifter så att webb program servern kan upprätta en säker anslutning till AD FS-servern. Autentiseringsuppgifterna måste vara för ett lokalt administratörs konto på AD FS-servern.

![Skärm bild som visar sidan "autentiseringsuppgifter". Administratörsautentiseringsuppgifter anges i fältet username och fältet lösen ord.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Ange tjänstkontot för AD FS-tjänsten
Tjänsten AD FS kräver ett domän tjänst konto för att autentisera användare och söka efter användar information i Active Directory. Två typer av tjänstkonton stöds:

* **Grupphanterat tjänst konto**: den här konto typen introducerades i AD DS av Windows Server 2012. Den här typen av konto tillhandahåller tjänster som AD FS. Det är ett enda konto där du inte behöver uppdatera lösen ordet regelbundet. Använd det här alternativet om du redan har Windows Server 2012-domänkontrollanter i den domän som AD FS-servrarna tillhör.
* **Domän användar konto**: den här typen av konto kräver att du anger ett lösen ord och uppdaterar det regelbundet när det upphör att gälla. Använd bara det här alternativet om du inte har Windows Server 2012-domänkontrollanter i den domän som AD FS-servrarna tillhör.

Om du har valt **skapa ett grupphanterat tjänst konto** och den här funktionen aldrig har använts i Active Directory anger du företags administratörens autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att initiera nyckelarkivet och aktivera funktionen i Active Directory.

> [!NOTE]
> Azure AD Connect kontrollerar om AD FSs tjänsten redan är registrerad som tjänstens huvud namn (SPN) i domänen.  Azure AD DS tillåter inte att dubbla SPN-poster registreras samtidigt.  Om ett duplicerat SPN hittas kan du inte fortsätta ytterligare förrän SPN tas bort.

![Skärm bild som visar sidan "A D F S tjänst konto".](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Välj den Azure AD-domän som du vill federera
Använd **Azure AD-domän** sidan för att konfigurera Federations relationen mellan AD FS och Azure AD. Här konfigurerar du AD FS att tillhandahålla säkerhetstoken till Azure AD. Du konfigurerar också Azure AD att lita på tokens från den här AD FS-instansen. 

På den här sidan kan du bara konfigurera en enda domän i den första installationen. Du kan konfigurera flera domäner senare genom att köra Azure AD Connect igen.

![Skärm bild som visar sidan "Azure A D Domain".](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Verifiera Azure AD-domänen som valts för federation
När du väljer den domän som du vill federera innehåller Azure AD Connect information som du kan använda för att verifiera en overifierad domän. Mer information finns i [lägga till och verifiera domänen](../fundamentals/add-custom-domain.md).

![Skärm bild som visar sidan "Azure A D Domain", inklusive information som du kan använda för att verifiera domänen.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect försöker verifiera domänen under konfigurations fasen. Om du inte lägger till nödvändiga Domain Name System (DNS)-poster kan konfigurationen inte slutföras.
>


## <a name="configuring-federation-with-pingfederate"></a>Konfigurera federation med PingFederate
Du kan konfigurera PingFederate med Azure AD Connect på bara några få klick. Följande krav måste vara uppfyllda:
- PingFederate 8,4 eller senare.  Mer information finns i [PingFederate-integrering med Azure Active Directory och Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Ett TLS/SSL-certifikat för det Federations tjänst namn som du vill använda (till exempel sts.contoso.com).

### <a name="verify-the-domain"></a>Verifiera domänen
När du har valt att konfigurera federationen med hjälp av PingFederate uppmanas du att verifiera den domän som du vill federera.  Välj domän på den nedrullningsbara menyn.

![Skärm bild som visar sidan "Azure A D Domain". Exempel domänen "contoso.com" är markerad.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Exportera PingFederate-inställningar


Konfigurera PingFederate som Federations Server för varje federerad Azure-domän.  Välj **Exportera inställningar** för att dela den här informationen med PingFederate-administratören.  Federations Server administratören uppdaterar konfigurationen och tillhandahåller PingFederate-serverns URL och port nummer så att Azure AD Connect kan verifiera inställningarna för metadata.  

![Skärm bild som visar sidan "PingFederate-inställningar". Knappen "Exportera inställningar" visas längst upp på sidan.](./media/how-to-connect-install-custom/ping2.png)

Kontakta PingFederate-administratören för att lösa eventuella verifieringsproblem.  Följande bild visar information om en PingFederate-server som inte har någon giltig förtroende relation med Azure.

![Skärm bild som visar Server information: PingFederate-servern hittades, men tjänst leverantörs anslutningen för Azure saknas eller är inaktive rad.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Verifiera federationsanslutning
Azure AD Connect försöker verifiera de autentiserings slut punkter som den hämtar från PingFederate metadata i föregående steg.  Azure AD Connect första försöken att lösa slut punkterna med hjälp av dina lokala DNS-servrar.  Därefter försöker den matcha slut punkterna med hjälp av en extern DNS-Provider.  Kontakta PingFederate-administratören för att lösa eventuella verifieringsproblem.  

![Skärm bild som visar sidan "verifiera anslutning".](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Verifiera Federations inloggning
Slutligen kan du kontrollera det nyligen konfigurerade federerade inloggningsflödet genom att logga in på den federerade domänen. Om inloggningen lyckas har federationen med PingFederate kon figurer ATS.

![Skärm bild som visar sidan "verifiera federerad inloggning". Ett meddelande längst ned visar att inloggningen lyckades.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Konfigurera och verifiera sidor
Konfigurationen sker på sidan **Konfigurera** .

> [!NOTE]
> Om du har konfigurerat Federation kontrollerar du att du även har konfigurerat [namn matchning för Federations Servrar](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) innan du fortsätter med installationen.
>



![Skärm bild som visar sidan "klar att konfigurera".](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Använd mellanlagrings läge
Det är möjligt att konfigurera en ny synkroniserad Server parallellt med mellanlagrings läge. Om du vill använda den här installationen kan bara en Sync-Server exportera till en katalog i molnet. Men om du vill flytta från en annan server, till exempel en server som kör DirSync, kan du aktivera Azure AD Connect i mellanlagrings läge. 

När du aktiverar mellanlagrings installationen importerar och synkroniserar Synkroniseringsmotorn data som vanligt. Men den exporterar inga data till Azure AD eller Active Directory. I mellanlagrings läge inaktive ras funktionen för synkronisering av lösen ord och tillbakaskrivning av lösen ord.

![Skärm bild som visar alternativet "Aktivera mellanlagrings läge".](./media/how-to-connect-install-custom/stagingmode.png)

I mellanlagrings läge kan du göra nödvändiga ändringar i Synkroniseringsmotorn och granska vad som kommer att exporteras. När konfigurationen ser bra ut kör du installationsguiden igen och inaktiverar mellanlagringsläge. 

Data exporteras nu till Azure AD från-servern. Se till att inaktivera den andra servern på samma gång så att endast en server exporterar aktivt.

Mer information finns i [Mellanlagringsläge](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Verifiera federationkonfigurationen
Azure AD Connect verifierar DNS-inställningarna när du väljer knappen **Verifiera** . Den kontrollerar följande inställningar:

* **Intranät anslutning**
    * Matcha fullständigt domän namn för federation: Azure AD Connect kontrollerar om DNS kan matcha federationens fullständiga domän namn för att säkerställa anslutning. Om Azure AD Connect inte kan matcha FQDN, Miss lyckas verifieringen. Slutför verifieringen genom att kontrol lera att det finns en DNS-post för Federations tjänstens FQDN.
    * DNS A-post: Azure AD Connect kontrollerar om Federations tjänsten har en A-post. Verifieringen Miss lyckas om en post saknas. Slutför verifieringen genom att skapa en A-post (inte en CNAME-post) för federationens FQDN.
* **Extra näts anslutning**
    * Matcha fullständigt domän namn för federation: Azure AD Connect kontrollerar om DNS kan matcha federationens fullständiga domän namn för att säkerställa anslutning.

      ![Skärm bild som visar sidan "installationen har slutförts".](./media/how-to-connect-install-custom/completed.png)

      ![Skärm bild som visar sidan "installationen har slutförts". Ett meddelande anger att intranäts konfigurationen har verifierats.](./media/how-to-connect-install-custom/adfs7.png)

Om du vill validera autentisering från slut punkt till slut punkt utför du en eller flera av följande tester manuellt:

* När synkroniseringen är klar använder du Azure AD Connect **Verifiera federerad inloggning** ytterligare för att verifiera autentisering för ett lokalt användar konto som du väljer.
* Se till att du kan logga in från en webbläsare från en domänansluten dator i intranätet. Anslut till https://myapps.microsoft.com . Använd sedan ditt inloggade konto för att verifiera inloggningen. Det inbyggda administratörs kontot för Azure AD DS är inte synkroniserat och du kan inte använda det för verifiering.
* Se till att du kan logga in från en enhet på extra nätet. Anslut till på en hem dator eller en mobil enhet https://myapps.microsoft.com . Ange sedan dina autentiseringsuppgifter.
* Verifiera inloggningen på en rich-klient. Anslut till https://testconnectivity.microsoft.com . Välj sedan **Office 365**  >  **Office 365 Single Sign-On-test**.

## <a name="troubleshoot"></a>Felsöka
Det här avsnittet innehåller felsöknings information som du kan använda om du har problem med att installera Azure AD Connect.

När du anpassar en Azure AD Connect-installation går du till sidan **installera nödvändiga komponenter** och väljer **använd en befintlig SQL Server**. Följande fel kan visas: "ADSync-databasen innehåller redan data och kan inte skrivas över. Ta bort den befintliga databasen och försök igen. "

![Skärm bild som visar sidan "installera nödvändiga komponenter". Ett fel visas längst ned på sidan.](./media/how-to-connect-install-custom/error1.png)

Du ser det här felet eftersom det redan finns en databas med namnet *ADSync* på SQL-instansen med SQL Server som du har angett.

Du ser vanligt vis det här felet när du har avinstallerat Azure AD Connect.  Databasen tas inte bort från datorn som kör SQL Server när du avinstallerar Azure AD Connect.

Så här löser du det här problemet:

1. Kontrol lera ADSync-databasen som Azure AD Connect användes innan den avinstallerades. Kontrol lera att databasen inte längre används.

2. Säkerhetskopiera databasen.

3. Ta bort databasen:
    1. Använd **Microsoft SQL Server Management Studio** för att ansluta till SQL-instansen. 
    1. Leta upp **ADSync** -databasen och högerklicka på den.
    1. I snabb menyn väljer du **ta bort**.
    1. Klicka på **OK** för att ta bort databasen.

![Skärm bild som visar Microsoft SQL Server Management Studio. En D-synkronisering har valts.](./media/how-to-connect-install-custom/error2.png)

När du har tagit bort ADSync-databasen väljer du **Installera** för att försöka installera igen.

## <a name="next-steps"></a>Nästa steg
När installationen är klar loggar du ut från Windows. Logga sedan in igen innan du använder Synchronization Service Manager eller regel redigeraren för synkronisering.

Nu när du har installerat Azure AD Connect kan du [Verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).

Mer information om de funktioner som du har aktiverat under installationen finns i [förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).

Mer information om andra vanliga ämnen finns i [Azure AD Connect Sync: Scheduler](how-to-connect-sync-feature-scheduler.md) och [integrera dina lokala identiteter med Azure AD](whatis-hybrid-identity.md).
