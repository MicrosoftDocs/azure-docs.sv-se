---
title: Skapa en elastisk åtkomst kontroll hanterings strategi – Azure AD
description: Det här dokumentet ger vägledning om strategier som en organisation bör vidta för att ge återhämtning för att minska risken för utelåsning vid oförutsedda avbrott
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7e4d0c41990fcc23dd19b5682997f6381bfdb20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937101"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Skapa en elastisk strategi för hantering av åtkomst kontroll med Azure Active Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Eftersom Microsoft måste svara på ändrade marknads villkor bör det inte tolkas som ett åtagande från Microsoft, och Microsoft kan inte garantera riktigheten av information som presenteras efter publicerings datumet.

Organisationer som förlitar sig på en enskild åtkomst kontroll, t. ex. Multi-Factor Authentication (MFA) eller en enda nätverks plats, för att skydda sina IT-system är utsatta för att få åtkomst till problem med sina appar och resurser om den enskilda åtkomst kontrollen blir otillgänglig eller felkonfigureras. En natur katastrof kan till exempel leda till otillgängligheten för stora segment av infrastruktur för telekommunikation eller företags nätverk. Ett sådant avbrott kan förhindra att slutanvändare och administratörer kan logga in.

Det här dokumentet ger vägledning om strategier som en organisation bör vidta för att ge återhämtning för att minska risken för utelåsning under oförutsedda avbrott i följande scenarier:

 1. Organisationer kan öka sin återhämtning för att minska risken för utelåsning **innan avbrott** genom att implementera strategier för minskning eller katastrof planer.
 2. Organisationer kan fortsätta att komma åt appar och resurser som de väljer **under ett avbrott** genom att ha hjälp av strategier och katastrof planer på plats.
 3. Organisationer bör se till att de bevarar information, t. ex. loggar,  **efter ett avbrott** och innan de återställer eventuella eventualförpliktelser som de har implementerat.
 4. Organisationer som inte har implementerat förebyggande strategier eller alternativa planer kan kunna implementera **nöd alternativ** för att hantera störningar.

## <a name="key-guidance"></a>Nyckel vägledning

Det finns fyra viktiga takeaways i det här dokumentet:

* Undvik administratörs utelåsning med hjälp av konton för nöd åtkomst.
* Implementera MFA med villkorlig åtkomst snarare än MFA per användare.
* Minska användar utelåsning genom att använda flera kontroller för villkorlig åtkomst.
* Minska användar utelåsning genom att tillhandahålla flera autentiseringsmetoder eller motsvarande för varje användare.

## <a name="before-a-disruption"></a>Före ett avbrott

Att begränsa ett verkligt avbrott måste vara en organisations primära fokus för hantering av åtkomst kontroll problem som kan uppstå. Genom att minska kan du planera en faktisk händelse och implementera strategier för att se till att åtkomst kontroller och åtgärder inte påverkas under avbrott.

### <a name="why-do-you-need-resilient-access-control"></a>Varför behöver du elastisk åtkomst kontroll?

 Identitet är kontroll planet för användare som har åtkomst till appar och resurser. Ditt identitets system styr vilka användare och under vilka villkor, till exempel åtkomst kontroller eller autentiseringskrav, får åtkomst till programmen. När en eller flera krav för autentisering eller åtkomst kontroll inte är tillgängliga för att användare ska kunna autentisera på grund av oförutsedda omständigheter, kan organisationer uppleva ett eller båda av följande problem:

* **Administratörs utelåsning:** Administratörer kan inte hantera klienten eller tjänsterna.
* **Användar utelåsning:** Användare kan inte komma åt appar eller resurser.

### <a name="administrator-lockout-contingency"></a>Katastrof för administratörs utelåsning

Om du vill låsa upp administratörs åtkomsten till din klient bör du skapa konton för nöd åtkomst. Dessa återställnings konton, även kallade *Break glas* -konton, tillåter åtkomst till hantering av Azure AD-konfiguration när vanliga åtkomst procedurer för privilegierade konton inte är tillgängliga. Minst två återställnings konton för nöd situationer bör skapas efter [konto rekommendationer för nöd åtkomst]( ../users-groups-roles/directory-emergency-access.md).

### <a name="mitigating-user-lockout"></a>Begränsa användar utelåsning

 Du kan minska risken för användar utelåsning genom att använda principer för villkorlig åtkomst med flera kontroller för att ge användarna möjlighet att välja hur de kommer åt appar och resurser. Genom att ge en användare valmöjligheten mellan, till exempel att logga in med MFA **eller** logga in från en hanterad enhet **eller** logga in från företags nätverket, om någon av åtkomst kontrollerna inte är tillgänglig har användaren andra alternativ för att fortsätta att fungera.

#### <a name="microsoft-recommendations"></a>Microsoft-rekommendationer

Inkludera följande åtkomst kontroller i dina befintliga principer för villkorlig åtkomst för organisationen:

1. Etablera flera autentiseringsmetoder för varje användare som förlitar sig på olika kommunikations kanaler, till exempel Microsoft Authenticator app (Internet-baserad), OATH-token (genereras på enheten) och SMS (telefoni). Följande PowerShell-skript hjälper dig att identifiera i förväg, vilka ytterligare metoder som dina användare bör registrera: [skript för Azure AD MFA-autentisering av autentiseringsmetod](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).
2. Distribuera Windows Hello för företag på Windows 10-enheter för att uppfylla MFA-krav direkt från enhets inloggning.
3. Använd betrodda enheter via [Azure AD hybrid Join](../devices/overview.md) eller [Microsoft Intune hanterade enheter](/intune/planning-guide). Betrodda enheter förbättrar användar upplevelsen, eftersom den betrodda enheten kan uppfylla kraven för stark autentisering av principer utan MFA-utmaning för användaren. MFA kommer sedan att krävas när du registrerar en ny enhet och vid åtkomst till appar eller resurser från ej betrodda enheter.
4. Använd Azure AD Identity Protection-riskfyllda principer som förhindrar åtkomst när användaren eller inloggningen är utsatt för risk i stället för fasta MFA-principer.
5. Om du skyddar VPN-åtkomst med Azure AD MFA NPS-tillägget bör du använda federering av VPN-lösningen som en [SAML-app](../manage-apps/view-applications-portal.md) och fastställa kategorin app enligt rekommendationerna nedan. 

>[!NOTE]
> Riskfyllda principer kräver [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) -licenser.

I följande exempel beskrivs principer som du måste skapa för att ge en elastisk åtkomst kontroll som användare kan använda för att komma åt sina appar och resurser. I det här exemplet behöver du en säkerhets grupp **AppUsers** med de mål användare som du vill ge åtkomst till, en grupp med namnet **CoreAdmins** med de viktigaste administratörerna och en grupp med namnet **EmergencyAccess** med kontona för nöd åtkomst.
Den här exempel princip uppsättningen beviljar valda användare i **AppUsers**, åtkomst till valda appar om de ansluter från en betrodd enhet eller ger stark autentisering, till exempel MFA. Den utesluter nödfalls konton och kärn administratörer.

**Principer för certifikat utfärdare som har angetts:**

* Princip 1: blockera åtkomst till personer utanför mål grupper
  * Användare och grupper: inkludera alla användare. Undanta AppUsers, CoreAdmins och EmergencyAccess
  * Molnappar: inkludera alla appar
  * Villkor: (ingen)
  * Bevilja kontroll: blockera
* Princip 2: bevilja åtkomst till AppUsers som kräver MFA eller betrodd enhet.
  * Användare och grupper: inkludera AppUsers. Uteslut CoreAdmins och EmergencyAccess
  * Molnappar: inkludera alla appar
  * Villkor: (ingen)
  * Bevilja kontroll: bevilja åtkomst, kräver Multi-Factor Authentication, kräver att enheten är kompatibel. För flera kontroller: Kräv en av de valda kontrollerna.

### <a name="contingencies-for-user-lockout"></a>Eventualförpliktelser/eventualtillgångar för användar utelåsning

Alternativt kan din organisation också skapa katastrof principer. För att skapa katastrof principer måste du definiera kompromiss kriterier mellan affärs kontinuitet, drifts kostnader, ekonomisk kostnad och säkerhets risker. Du kan till exempel bara aktivera en katastrof princip för en delmängd av användare, för en delmängd av appar, för en delmängd av klienter eller från en delmängd av platser. Katastrof principer ger administratörer och slutanvändare åtkomst till appar och resurser under ett avbrott när ingen minsknings metod implementerades. Microsoft rekommenderar att du aktiverar katastrof principer i [endast rapport läge](../conditional-access/howto-conditional-access-insights-reporting.md) när de inte används så att administratörer kan övervaka den potentiella effekten av principerna om de måste vara påslagna.

 Att förstå exponeringen under ett avbrott bidrar till att minska risken och är en viktig del av planerings processen. För att skapa en katastrof plan måste du först fastställa följande affärs krav för din organisation:

1. Fastställ dina verksamhets kritiska appar i förväg: vilka appar måste du ge åtkomst till, till och med en lägre risk-/säkerhets position? Skapa en lista över de här apparna och se till att dina andra intressenter (företag, säkerhet, juridiskt, ledarskap) alla accepterar att om all åtkomst kontroll går bort måste dessa appar fortfarande fortsätta att köras. Du kommer troligen att bli klar med kategorier av:
   * **Kategori 1 verksamhets kritiska appar** som inte kan vara tillgängliga i mer än några minuter, till exempel appar som direkt påverkar organisationens intäkter.
   * **Kategori 2 viktiga appar** som företaget behöver vara åtkomliga inom några timmar.
   * **Kategori 3-appar med låg prioritet** som kan motstå avbrott i några dagar.
2. För appar i kategori 1 och 2 rekommenderar Microsoft dig att planera vilken typ av åtkomst nivå du vill tillåta:
   * Vill du tillåta fullständig åtkomst eller begränsad session, t. ex. begränsa hämtningen?
   * Vill du tillåta åtkomst till en del av appen men inte till hela appen?
   * Vill du tillåta informations Worker-åtkomst och blockera administratörs åtkomst tills åtkomst kontrollen har återställts?
3. För de här apparna rekommenderar Microsoft också att du planerar vilka vägar som du kommer att öppna och vilka du kommer att stänga:
   * Vill du tillåta att webbläsaren endast får åtkomst till och blockerar rika klienter som kan spara offlinedata?
   * Vill du bara tillåta åtkomst för användare i företags nätverket och hålla användare blockerade utanför nätverket?
   * Vill du tillåta åtkomst från vissa länder eller regioner endast under drifts störningar?
   * Vill du använda principer för katastrof principer, särskilt för verksamhets kritiska appar, för att Miss lyckas eller lyckas om det inte finns någon alternativ åtkomst kontroll?

#### <a name="microsoft-recommendations"></a>Microsoft-rekommendationer

En princip för en beredskaps villkorlig åtkomst är en **säkerhets kopierings princip** som inte utesluter Azure AD MFA, MFA, riskhantering eller enhets kontroller. För att minimera oväntade avbrott när en katastrof princip aktive ras bör principen förbli i endast rapport läge när den inte används. Administratörer kan övervaka de potentiella effekterna av sina katastrof principer med hjälp av arbets boken för villkorlig åtkomst. När din organisation bestämmer sig för att aktivera din katastrof plan kan administratörer aktivera principen och inaktivera de vanliga kontrollbaserade principerna.

>[!IMPORTANT]
> Att inaktivera principer som upprätthåller säkerheten för dina användare, och som tillfälligt, minskar din säkerhets position medan beredskaps planen är på plats.

* Konfigurera en uppsättning återställnings principer om ett avbrott i en typ av autentiseringsuppgift eller en mekanism för åtkomst kontroll påverkar åtkomsten till dina appar. Konfigurera en princip i endast rapport tillstånd som kräver domän anslutning som en kontroll, som en säkerhets kopia för en aktiv princip som kräver en MFA-provider från tredje part.
* Minska risken för dåliga aktörer som gissar lösen ord, om MFA inte krävs, genom att följa anvisningarna i guiden för [lösen Ords vägledning](https://aka.ms/passwordguidance) White Paper.
* Distribuera [Azure ad Self-Service lösen ords återställning (SSPR)](./tutorial-enable-sspr.md) och [Azure AD Password Protection](./howto-password-ban-bad-on-premises-deploy.md) för att se till att användarna inte använder vanliga lösen ord och villkor som du väljer att förbjuda.
* Använd principer som begränsar åtkomsten i apparna om en viss autentiseringsnivå inte uppnås i stället för att bara komma tillbaka till fullständig åtkomst. Exempel:
  * Konfigurera en säkerhets kopierings princip som skickar anspråk för begränsad session till Exchange och SharePoint.
  * Om din organisation använder Microsoft Cloud App Security bör du överväga att återgå till en princip som samverkar med MCAS och sedan MCAS tillåter skrivskyddad åtkomst men inte uppladdning.
* Namnge dina principer för att se till att det är enkelt att hitta dem under ett avbrott. Inkludera följande element i princip namnet:
  * Ett *etikett nummer* för principen.
  * Den text som ska visas är den här principen endast för nödfall. Till exempel: **Aktivera i nödfall**
  * Det *avbrott* som det gäller för. Exempel: **under MFA-avbrott**
  * Ett *sekvensnummer* för att Visa ordningen du måste aktivera principerna.
  * De *appar* som den gäller för.
  * De *kontroller* som ska tillämpas.
  * De *villkor* som krävs.
  
Den här namngivnings standarden för katastrof principer är följande: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Följande exempel: **exempel på en-beredskaps princip för villkorlig åtkomst för att återställa åtkomsten till verksamhets kritiska samarbets program**, är en typisk företags katastrof. I det här scenariot kräver organisationen vanligt vis MFA för all åtkomst till Exchange Online och SharePoint Online, och störningar i det här fallet är att MFA-providern för kunden har ett avbrott (om Azure AD MFA, lokala MFA-provider eller tredjeparts MFA). Den här principen minimerar detta avbrott genom att tillåta specifika mål användare åtkomst till dessa appar från betrodda Windows-enheter endast när de ansluter till appen från ett betrott företags nätverk. Det kommer också att utesluta nöd konton och kärn administratörer från dessa begränsningar. Mål användarna får sedan åtkomst till Exchange Online och SharePoint Online medan andra användare fortfarande inte har åtkomst till apparna på grund av avbrottet. I det här exemplet krävs en namngiven nätverks plats **CorpNetwork** och en säkerhets grupp **ContingencyAccess** med mål användarna, en grupp med namnet **CoreAdmins** med kärn administratörer och en grupp med namnet **EmergencyAccess** med kontona för nöd åtkomst. Katastrofen kräver fyra principer för att ge önskad åtkomst. 

**Exempel på en beredskaps princip för villkorlig åtkomst för att återställa åtkomsten till verksamhets kritiska samarbets program:**

* Princip 1: Kräv domänanslutna enheter för Exchange och SharePoint
  * Namn: EM001 – aktivera i nödfall: MFA-avbrott [1/4]-Exchange SharePoint – Kräv hybrid Azure AD-anslutning
  * Användare och grupper: inkludera ContingencyAccess. Uteslut CoreAdmins och EmergencyAccess
  * Molnappar: Exchange Online och SharePoint Online
  * Villkor: alla
  * Bevilja kontroll: Kräv domänanslutna
  * Tillstånd: endast rapport
* Princip 2: blockera andra plattformar än Windows
  * Namn: EM002 – aktivera i nödfall: MFA-avbrott [2/4]-Exchange SharePoint-blockera åtkomst förutom Windows
  * Användare och grupper: inkludera alla användare. Uteslut CoreAdmins och EmergencyAccess
  * Molnappar: Exchange Online och SharePoint Online
  * Villkor: enhets plattformen omfattar alla plattformar, undanta Windows
  * Bevilja kontroll: blockera
  * Tillstånd: endast rapport
* Princip 3: blockera nätverk förutom CorpNetwork
  * Namn: EM003 – aktivera i nödfall: MFA-avbrott [3/4]-Exchange SharePoint-blockera åtkomst förutom företags nätverk
  * Användare och grupper: inkludera alla användare. Uteslut CoreAdmins och EmergencyAccess
  * Molnappar: Exchange Online och SharePoint Online
  * Villkor: platser omfattar alla platser, exkludera CorpNetwork
  * Bevilja kontroll: blockera
  * Tillstånd: endast rapport
* Princip 4: blockera EAS explicit
  * Namn: EM004 – aktivera i nödfall: MFA-avbrott [4/4]-Exchange-blockera EAS för alla användare
  * Användare och grupper: inkludera alla användare
  * Molnappar: inkludera Exchange Online
  * Villkor: klient program: Exchange Active Sync
  * Bevilja kontroll: blockera
  * Tillstånd: endast rapport

Aktiverings ordning:

1. Undanta ContingencyAccess, CoreAdmins och EmergencyAccess från den befintliga MFA-principen. Verifiera att en användare i ContingencyAccess har åtkomst till SharePoint Online och Exchange Online.
2. Aktivera princip 1: verifiera att användare på domänanslutna enheter som inte finns i exkluderings grupper kan komma åt Exchange Online och SharePoint Online. Verifiera att användare i gruppen exkludera har åtkomst till SharePoint Online och Exchange från vilken enhet som helst.
3. Aktivera princip 2: verifiera att användare som inte ingår i exkluderings gruppen inte kan komma åt SharePoint Online och Exchange Online från sina mobila enheter. Verifiera att användare i gruppen exkludera kan komma åt SharePoint och Exchange från valfri enhet (Windows/iOS/Android).
4. Aktivera princip 3: kontrol lera att användare som inte finns i exkluderings grupper inte kan komma åt SharePoint och Exchange från företags nätverket, även med en domänansluten dator. Verifiera att användare i gruppen exkludera kan komma åt SharePoint och Exchange från alla nätverk.
5. Aktivera princip 4: kontrol lera att alla användare inte kan hämta Exchange Online från de interna e-postapparna på mobila enheter.
6. Inaktivera den befintliga MFA-principen för SharePoint Online och Exchange Online.

I det här exemplet i det här exemplet, **exempel på principer för villkorlig åtkomst för att ge mobil åtkomst till Salesforce**, återställs åtkomsten till affärsappar. I det här scenariot kräver kunden vanligt vis att deras Sälj personal har åtkomst till Salesforce (konfigurerad för enkel inloggning med Azure AD) från mobila enheter för att endast tillåtas från kompatibla enheter. Störningar i det här fallet är att det är problem med att utvärdera enhetens efterlevnad och avbrottet sker vid en känslig tid där Sälj teamet behöver åtkomst till Salesforce för att avsluta avtal. Dessa policyer för katastrofer ger viktiga användare åtkomst till Salesforce från en mobil enhet så att de kan fortsätta att avsluta avtal och inte störa verksamheten. I det här exemplet innehåller **SalesforceContingency** alla Sälj medarbetare som behöver behålla åtkomst och **SalesAdmins** innehåller nödvändiga administratörer för Salesforce.

**Exempel på principer för villkorlig åtkomst för katastrofer:**

* Princip 1: blockera alla som inte är med i SalesContingency-teamet
  * Namn: EM001 – aktivera i nödfall: störning av enhetskompatibilitet [1/2]-Salesforce-blockera alla användare utom SalesforceContingency
  * Användare och grupper: inkludera alla användare. Uteslut SalesAdmins och SalesforceContingency
  * Molnappar: Salesforce.
  * Villkor: Inga
  * Bevilja kontroll: blockera
  * Tillstånd: endast rapport
* Princip 2: blockera försäljnings teamet från vilken plattform som helst än mobil (för att minska angrepps området)
  * Namn: EM002 – aktivera i nödfall: störning av enhetskompatibilitet [2/2]-Salesforce-blockera alla plattformar utom iOS och Android
  * Användare och grupper: inkludera SalesforceContingency. Uteslut SalesAdmins
  * Molnappar: Salesforce
  * Villkor: enhets plattformen omfattar alla plattformar, undanta iOS och Android
  * Bevilja kontroll: blockera
  * Tillstånd: endast rapport

Aktiverings ordning:

1. Undanta SalesAdmins och SalesforceContingency från den befintliga efterlevnadsprinciper för enheter för Salesforce. Verifiera att en användare i SalesforceContingency-gruppen har åtkomst till Salesforce.
2. Aktivera princip 1: kontrol lera att användare utanför SalesContingency inte har åtkomst till Salesforce. Kontrol lera att användarna i SalesAdmins och SalesforceContingency har åtkomst till Salesforce.
3. Aktivera princip 2: verifiera att användare i gruppen SalesContingency inte kan komma åt Salesforce från sina Windows/Mac-datorer, men kan fortfarande komma åt från sina mobila enheter. Kontrol lera att SalesAdmin fortfarande har åtkomst till Salesforce från vilken enhet som helst.
4. Inaktivera den befintliga efterlevnadsprinciper för enheter för Salesforce.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>Katastrofer för användar utelåsning från lokal resurser (NPS-tillägg)

Om du skyddar VPN-åtkomst med Azure AD MFA NPS-tillägget bör du använda federering av VPN-lösningen som en [SAML-app](../manage-apps/view-applications-portal.md) och fastställa kategorin app enligt rekommendationerna nedan. 

Om du har distribuerat Azure AD MFA NPS-tillägget för att skydda lokal-resurser, t. ex. VPN och fjärr skrivbords-Gateway, med MFA, bör du överväga i förväg om du är redo att inaktivera MFA i ett fall av nödfall.

I det här fallet kan du inaktivera NPS-tillägget, vilket innebär att NPS-servern endast verifierar den primära autentiseringen och kommer inte att tillämpa MFA på användarna.

Inaktivera NPS-tillägg: 
-   Exportera register nyckeln HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters som en säkerhets kopia. 
-   Ta bort registervärdena för "AuthorizationDLLs" och "ExtensionDLLs", inte parametrar-nyckeln. 
-   Starta om tjänsten Network Policy Service (IAS) för att ändringarna ska börja gälla 
-   Ta reda på om den primära autentiseringen för VPN har slutförts.

När tjänsten har återställts och du är redo att tillämpa MFA på dina användare igen, aktiverar du NPS-tillägget: 
-   Importera register nyckeln från säkerhets kopierings HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters 
-   Starta om tjänsten Network Policy Service (IAS) för att ändringarna ska börja gälla 
-   Kontrol lera om den primära autentiseringen och den sekundära autentiseringen för VPN har slutförts.
-   Granska NPS-servern och VPN-loggen för att avgöra vilka användare som har loggat in i nödfalls fönstret.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Distribuera hash-synkronisering av lösen ord även om du är federerad eller använder direktautentisering

Användar utelåsning kan också ske om följande villkor är uppfyllda:

- Din organisation använder en hybrid identitets lösning med direkt autentisering eller Federation.
- Dina lokala identitets system (till exempel Active Directory, AD FS eller beroende komponent) är inte tillgängliga. 
 
För att vara mer elastisk bör din organisation [Aktivera synkronisering av lösen ords-hash](../hybrid/choose-ad-authn.md), eftersom det gör att du kan [Växla till att använda hash-synkronisering av lösen ord](../hybrid/plan-connect-user-signin.md) om dina lokala identitets system är nere.

#### <a name="microsoft-recommendations"></a>Microsoft-rekommendationer
 Aktivera hash-synkronisering av lösen ord med hjälp av guiden Azure AD Connect, oavsett om din organisation använder Federation eller direktautentisering.

>[!IMPORTANT]
> Det är inte nödvändigt att konvertera användare från federerade till hanterad autentisering till att använda hash-synkronisering av lösen ord.

## <a name="during-a-disruption"></a>Under ett avbrott

Om du har valt att implementera en minsknings plan kan du automatiskt överleva ett enskilt avbrott i åtkomst kontrollen. Men om du har valt att skapa en katastrof plan kan du aktivera dina katastrof principer under återavbrotten av åtkomst kontrollen:

1. Aktivera dina katastrof principer som ger mål användare åtkomst till specifika appar från specifika nätverk.
2. Inaktivera dina vanliga kontrollbaserade principer.

### <a name="microsoft-recommendations"></a>Microsoft-rekommendationer

Beroende på vilka åtgärder som används under ett avbrott kan din organisation bevilja åtkomst med bara lösen ord. Ingen skydds åtgärd är en stor säkerhets risk som måste vägas noggrant. Organisationerna måste:

1. Som en del av din strategi för ändrings kontroll måste du dokumentera varje ändring och det tidigare läget så att du kan återställa eventuella eventualförpliktelser som du har implementerat så snart åtkomst kontrollerna är helt operativa.
2. Anta att skadliga aktörer kommer att försöka att skörda lösen ord genom lösen ords spridning eller nätfiske-attacker när du har inaktiverat MFA. Dessutom kanske Felaktiga aktörer redan har lösen ord som tidigare inte beviljade åtkomst till någon resurs som kan provas under det här fönstret. För viktiga användare som chefer, kan du delvis minimera den här risken genom att återställa sina lösen ord innan du inaktiverar MFA för dem.
3. Arkivera all inloggnings aktivitet för att identifiera vem som har åtkomst till vad under tiden MFA har inaktiverats.
4. [Prioritering alla risk identifieringar som rapporter ATS](../reports-monitoring/concept-sign-ins.md) i det här fönstret.

## <a name="after-a-disruption"></a>Efter ett avbrott

Ångra de ändringar som du har gjort som en del av den aktiverade beredskaps planen när tjänsten har återställts som orsakade avbrott. 

1. Aktivera vanliga principer
2. Inaktivera dina katastrof principer tillbaka till endast rapport läge. 
3. Återställa andra ändringar som du har gjort och dokumenterat under avbrott.
4. Om du har använt ett konto för nödfalls åtkomst måste du komma ihåg att återskapa autentiseringsuppgifter och fysiskt skydda de nya autentiseringsuppgifterna som en del av dina konto procedurer för nöd åtkomst.
5. Fortsätt att [prioritering alla risk identifieringar som rapporter ATS](../reports-monitoring/concept-sign-ins.md) efter avbrott för misstänkt aktivitet.
6. Återkalla alla uppdateringstoken som utfärdats [med PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) för att rikta in dig på en uppsättning användare. Att återkalla alla uppdateringstoken är viktigt för privilegierade konton som används under avbrottet och det tvingar dem att autentisera och uppfylla kontrollen över de återställda principerna.

## <a name="emergency-options"></a>Nöd alternativ

 Om det är en nöd situation och din organisation inte tidigare har implementerat en minskning eller en katastrof plan följer du rekommendationerna i avsnittet om [beredskap för användar utelåsning](#contingencies-for-user-lockout) om de redan använder principer för villkorlig åtkomst för att genomdriva MFA.
Om din organisation använder äldre MFA-principer per användare, kan du överväga följande alternativ:

1. Om du har företags nätverkets utgående IP-adress kan du lägga till dem som betrodda IP-adresser för att endast aktivera autentisering till företags nätverket.
   1. Om du inte har inventeringen av utgående IP-adresser, eller om du behöver aktivera åtkomst i och utanför företags nätverket, kan du lägga till hela IPv4-adress utrymmet som betrodda IP-adresser genom att ange 0.0.0.0/1 och 128.0.0.0/1.

>[!IMPORTANT]
 > Om du utökar de betrodda IP-adresserna för att blockera åtkomst kommer risk identifieringar som är associerade med IP-adresser (till exempel omöjliga resor eller okända platser) inte att genereras.

>[!NOTE]
 > Det går bara att konfigurera [betrodda IP-adresser](./howto-mfa-mfasettings.md) för Azure AD MFA med [Azure AD Premium licenser](./concept-mfa-licensing.md).

## <a name="learn-more"></a>Läs mer

* [Dokumentation om Azure AD-autentisering](./howto-mfaserver-iis.md)
* [Hantera administrativa konton för katastrof åtkomst i Azure AD](../roles/security-emergency-access.md)
* [Konfigurera namngivna platser i Azure Active Directory](../reports-monitoring/quickstart-configure-named-locations.md)
  * [Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings)
* [Konfigurera hybrid Azure Active Directory anslutna enheter](../devices/hybrid-azuread-join-plan.md)
* [Distributionsguide för Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Vägledning för lösen ord – Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Vad är villkor i Azure Active Directory villkorlig åtkomst?](../conditional-access/concept-conditional-access-conditions.md)
* [Vad är åtkomst kontroller i Azure Active Directory villkorlig åtkomst?](../conditional-access/controls.md)
* [Vad är läget endast i rapporten för villkorlig åtkomst?](../conditional-access/concept-conditional-access-report-only.md)
