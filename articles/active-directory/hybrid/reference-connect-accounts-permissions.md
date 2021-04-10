---
title: 'Azure AD Connect: konton och behörigheter | Microsoft Docs'
description: I det här avsnittet beskrivs de konton som används och har skapats och behörigheter som krävs.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f7466931537745fb188a3bdb05646bff19912e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466260"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Konton och behörigheter

## <a name="accounts-used-for-azure-ad-connect"></a>Konton som används för Azure AD Connect

![Översikt över konton](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect använder 3 konton för att synkronisera information från lokala eller Windows Server Active Directory till Azure Active Directory.  Dessa konton är:

- **AD DS-anslutnings konto**: används för att läsa/skriva information till Windows Server Active Directory

- **ADSync-tjänst konto**: används för att köra synkroniseringstjänsten och komma åt SQL-databasen

- **Azure AD Connector-konto**: används för att skriva information till Azure AD

Förutom dessa tre konton som används för att köra Azure AD Connect behöver du också följande ytterligare konton för att installera Azure AD Connect.  Dessa är:

- **Lokalt administratörs konto**: administratören som installerar Azure AD Connect och som har lokal administratörs behörighet på datorn.

- **AD DS Enterprise-administratörskonto**: används för att skapa "AD DS Connector-kontot" ovan.

- **Azure AD global administratörs konto**: används för att skapa Azure AD Connector-kontot och konfigurera Azure AD.  Du kan visa globala administratörs konton i Azure Portal.  Se [lista Azure AD-roll tilldelningar](../../active-directory/roles/view-assignments.md).

- **SQL sa-konto (valfritt)**: används för att skapa ADSync-databasen när du använder den fullständiga versionen av SQL Server.  Den här SQL Server kan vara lokal eller fjärran sluten till den Azure AD Connect installationen.  Kontot kan vara samma konto som företags administratören.  Etableringen av databasen kan nu utföras out-of-band av SQL-administratören och sedan installeras av Azure AD Connect administratör med databas ägar rättigheter.  Information om detta finns i [installera Azure AD Connect med hjälp av SQL-delegerad administratörs behörighet](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> Från och med build 1.4. # # #. # stöds det inte längre att använda ett företags administratörs konto eller ett domän administratörs konto som AD DS-konto.  Om du försöker ange ett konto som är företags administratör eller domän administratör när du anger **Använd befintligt konto** visas ett fel meddelande.

> [!NOTE]
> Det finns stöd för att hantera de administrativa konton som används i Azure AD Connect från en ESAE-administrativ skog (även kallad "Red skog").
> Dedikerade administrativa skogar tillåter organisationer att vara värdar för administrativa konton, arbetsstationer och grupper i en miljö som har starkare säkerhetskontroller än produktionsmiljön.
> Mer information om dedikerade administrativa skogar finns i [ESAE administrativ skog design metod](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> Den globala administratörs rollen krävs inte efter den första installationen och det enda obligatoriska kontot kommer att vara roll kontot för **Directory-synkronisering** . Det innebär inte nödvändigt vis att du bara vill ta bort kontot med rollen global administratör. Det är bättre att ändra rollen till en mindre kraftfull roll eftersom helt ta bort kontot kan orsaka problem om du skulle behöva köra guiden igen. Genom att minska behörigheten för rollen kan du alltid höja behörigheten igen om du måste använda guiden Azure AD Connect igen. 

## <a name="installing-azure-ad-connect"></a>Installerar Azure AD Connect
Installations guiden för Azure AD Connect har två olika sökvägar:

* I Express inställningar kräver guiden fler privilegier.  Detta är så att den kan konfigurera konfigurationen enkelt, utan att du behöver skapa användare eller konfigurera behörigheter.
* I anpassade inställningar erbjuder guiden fler alternativ och alternativ. Det finns dock vissa situationer där du måste se till att du har rätt behörighet.



## <a name="express-settings-installation"></a>Installation av Express inställningar
I Express-inställningar begär installations guiden följande:

  - Autentiseringsuppgifter för AD DS Enterprise-administratör
  - Autentiseringsuppgifter för Global Azure AD-administratör

### <a name="ad-ds-enterprise-admin-credentials"></a>Autentiseringsuppgifter för AD DS företags administratör
Kontot för AD DS Enterprise-administratören används för att konfigurera din lokala Active Directory. Dessa autentiseringsuppgifter används endast under installationen och används inte när installationen har slutförts. Företags administratören, inte domän administratören, bör se till att behörigheterna i Active Directory kan anges i alla domäner.

Om du uppgraderar från DirSync används autentiseringsuppgifterna för AD DS Enterprise-administratörer för att återställa lösen ordet för det konto som används av DirSync. Du måste också ha autentiseringsuppgifter för Global Azure AD-administratör.

### <a name="azure-ad-global-admin-credentials"></a>Autentiseringsuppgifter för Global Azure AD-administratör
Dessa autentiseringsuppgifter används endast under installationen och används inte när installationen har slutförts. Den används för att skapa Azure AD Connector-kontot som används för att synkronisera ändringar i Azure AD. Kontot aktiverar även synkronisering som en funktion i Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS-anslutnings konto krävde behörigheter för snabb inställningar
AD DS-anslutningsprogrammet skapas för läsning och skrivning till Windows Server AD och har följande behörigheter när de skapas med Express inställningar:

| Behörighet | Används för |
| --- | --- |
| <li>Replikera katalog ändringar</li><li>Replikera katalog ändringar alla |Hash-synkronisering av lösen ord |
| Läsa/skriva alla egenskaper användare |Importera och Exchange hybrid |
| Läsa/skriva alla egenskaper iNetOrgPerson |Importera och Exchange hybrid |
| Läsa/skriva alla egenskaps grupper |Importera och Exchange hybrid |
| Läs/Skriv alla egenskaper kontakt |Importera och Exchange hybrid |
| Återställa lösenord |Förberedelse för att aktivera tillbakaskrivning av lösen ord |

### <a name="express-installation-wizard-summary"></a>Guide Sammanfattning för Express installation

![Snabb installation](./media/reference-connect-accounts-permissions/express.png)

Följande är en sammanfattning av sidorna i guiden Express installation, autentiseringsuppgifterna som samlas in och vad de används för.

| Guide sida | Insamlade autentiseringsuppgifter | Behörigheter som krävs | Används för |
| --- | --- | --- | --- |
| Ej tillämpligt |Användare som kör installations guiden |Administratör för den lokala servern |<li>Skapar det ADSync-tjänstkonto som används för att köra synkroniseringstjänsten. |
| Anslut till Azure AD |Autentiseringsuppgifter för Azure AD-katalog |Global administratörs roll i Azure AD |<li>Aktiverar synkronisering i Azure AD-katalogen.</li>  <li>Skapandet av Azure AD Connector-kontot som används för pågående synkronisering i Azure AD.</li> |
| Anslut till AD DS |Lokala Active Directory autentiseringsuppgifter |Medlem i gruppen Enterprise administratörer (EA) i Active Directory |<li>Skapar AD DS-anslutningsprogrammet i Active Directory och beviljar behörigheter till det. Det här skapade kontot används för att läsa och skriva katalog information under synkroniseringen.</li> |


## <a name="custom-installation-settings"></a>Anpassade installations inställningar

Med installationen av anpassade inställningar får du fler alternativ och alternativ i guiden. 

### <a name="custom-installation-wizard-summary"></a>Sammanfattning av guiden Anpassad installation

Följande är en sammanfattning av sidorna för anpassade installations guider, autentiseringsuppgifterna som samlas in och vad de används för.

![Skärm bild som visar sidor med anpassad installations guide.](./media/reference-connect-accounts-permissions/customize.png)

| Guide sida | Insamlade autentiseringsuppgifter | Behörigheter som krävs | Används för |
| --- | --- | --- | --- |
| Ej tillämpligt |Användare som kör installations guiden |<li>Administratör för den lokala servern</li><li>Om du använder en fullständig SQL Server måste användaren vara system administratör (SA) i SQL</li> |Som standard skapar det lokala kontot som används som Synkroniseringsmotorn för synkroniseringstjänsten. Kontot skapas bara när administratören inte anger något visst konto. |
| Installera Synchronization Services, tjänst konto alternativ |Autentiseringsuppgifter för AD eller lokalt användar konto |Användare, behörigheter beviljas av installations guiden |Om administratören anger ett konto används det här kontot som tjänst konto för synkroniseringstjänsten. |
| Anslut till Azure AD |Autentiseringsuppgifter för Azure AD-katalog |Global administratörs roll i Azure AD |<li>Aktiverar synkronisering i Azure AD-katalogen.</li>  <li>Skapandet av Azure AD Connector-kontot som används för pågående synkronisering i Azure AD.</li> |
| Anslut dina kataloger |Lokala Active Directory autentiseringsuppgifter för varje skog som är ansluten till Azure AD |Behörigheterna beror på vilka funktioner du aktiverar och hur du hittar dem i skapa AD DS-anslutnings kontot |Det här kontot används för att läsa och skriva katalog information under synkroniseringen. |
| AD FS-servrar |För varje server i listan samlar guiden in autentiseringsuppgifter när inloggnings uppgifterna för användaren som kör guiden inte räcker för att ansluta |Domän administratör |Installation och konfiguration av AD FS server rollen. |
| Web Application Proxy-servrar |För varje server i listan samlar guiden in autentiseringsuppgifter när inloggnings uppgifterna för användaren som kör guiden inte räcker för att ansluta |Lokal administratör på mål datorn |Installation och konfiguration av WAP-serverrollen. |
| Autentiseringsuppgifter för proxy förtroende |Autentiseringsuppgifter för Federations tjänst förtroende (de autentiseringsuppgifter som proxyn använder för att registrera ett förtroende certifikat från FS |Domän konto som är en lokal administratör för den AD FS-servern |Första registrering av FS-WAP-förtroende certifikat. |
| Sidan AD FS tjänst konto, "Använd ett domän användar konto alternativ" |Autentiseringsuppgifter för AD-användarkonto |Domän användare |Det användar konto för Azure AD vars autentiseringsuppgifter har angetts används som inloggnings konto för AD FS tjänsten. |

### <a name="create-the-ad-ds-connector-account"></a>Skapa AD DS-anslutnings kontot

>[!IMPORTANT]
>En ny PowerShell-modul med namnet ADSyncConfig. psm1 introducerades med build **1.1.880.0** (lanserades i augusti 2018) som innehåller en samling cmdlets som hjälper dig att konfigurera rätt Active Directory behörigheter för Azure AD DS Connector-kontot.
>
>Mer information finns i [Azure AD Connect: Konfigurera behörighet för AD DS-kopplings konto](how-to-connect-configure-ad-ds-connector-account.md)

Det konto som du anger på sidan **Anslut dina kataloger** måste finnas i Active Directory före installationen.  Azure AD Connect version 1.1.524.0 och senare har möjlighet att låta guiden Azure AD Connect skapa det **AD DS-administratörskonto** som används för att ansluta till Active Directory.  

Den måste också ha de behörigheter som krävs. Installations guiden kontrollerar inte behörigheterna och eventuella problem hittas bara under synkroniseringen.

Vilka behörigheter du behöver beror på vilka valfria funktioner du aktiverar. Om du har flera domäner måste behörigheterna beviljas för alla domäner i skogen. Om du inte aktiverar någon av dessa funktioner räcker standard **domänens användar** behörigheter.

| Funktion | Behörigheter |
| --- | --- |
| ms-DS-ConsistencyGuid-funktion |Skriv behörigheter till attributet ms-DS-ConsistencyGuid dokumenterat i [design koncept – med MS-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Hash-synkronisering av lösen ord |<li>Replikera katalog ändringar</li>  <li>Replikera katalog ändringar alla |
| Exchange hybrid distribution |Skriv behörigheter till de attribut som dokumenteras i [Exchange hybrid tillbakaskrivning](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) för användare, grupper och kontakter. |
| Offentlig Exchange-e-postmapp |Läs behörighet till attributen som dokumenteras i den [offentliga Exchange-e-postmappen](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) för offentliga mappar. | 
| Tillbakaskrivning av lösenord |Skriv behörigheter till de attribut som dokumenteras i [komma igång med lösen ords hantering](../authentication/tutorial-enable-sspr-writeback.md) för användare. |
| Tillbakaskrivning av enheter |Behörigheter som beviljats med ett PowerShell-skript enligt beskrivningen i [tillbakaskrivning av enhet](how-to-connect-device-writeback.md). |
| Tillbakaskrivning av grupp |Gör att du kan ångra **Microsoft 365 grupper** till en skog med Exchange installerat.|

## <a name="upgrade"></a>Uppgradera
När du uppgraderar från en version av Azure AD Connect till en ny version behöver du följande behörigheter:

>[!IMPORTANT]
>Från och med build 1.1.484 introducerade Azure AD Connect ett Regressions fel som kräver sysadmin-behörighet för att uppgradera SQL-databasen.  Denna bugg korrigeras i build-1.1.647.  Om du uppgraderar till den här versionen måste du ha sysadmin-behörighet.  Dbo-behörigheter är inte tillräckliga.  Om du försöker uppgradera Azure AD Connect utan att ha sysadmin-behörighet kommer uppgraderingen att Miss lyckas och Azure AD Connect kommer inte längre att fungera korrekt efteråt.  Microsoft är medveten om detta och arbetar med att åtgärda detta.


| Huvudkonto | Behörigheter som krävs | Används för |
| --- | --- | --- |
| Användare som kör installations guiden |Administratör för den lokala servern |Uppdatera binärfiler. |
| Användare som kör installations guiden |Medlem i ADSyncAdmins |Ändra regler för synkronisering och annan konfiguration. |
| Användare som kör installations guiden |Om du använder en fullständig SQL Server: DBO (eller liknande) i Synkroniseringsmotorn |Gör ändringar på databas nivå, t. ex. uppdatering av tabeller med nya kolumner. |

## <a name="more-about-the-created-accounts"></a>Mer om de skapade kontona
### <a name="ad-ds-connector-account"></a>AD DS-anslutningskonto
Om du använder Express inställningar skapas ett konto i Active Directory som används för synkronisering. Det skapade kontot finns i skogs rot domänen i behållaren användare och har sitt namn prefixet med **MSOL_**. Kontot skapas med ett långt komplext lösen ord som inte upphör att gälla. Om du har en lösen ords princip i din domän kontrollerar du att det är tillåtet att använda långa och komplexa lösen ord för det här kontot.

![AD-konto](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Om du använder anpassade inställningar ansvarar du för att skapa kontot innan du påbörjar installationen.  Se skapa AD DS Connector-kontot.

### <a name="adsync-service-account"></a>ADSync-tjänstkonto
Synkroniseringstjänsten kan köras under olika konton. Den kan köras under ett **virtuellt tjänst konto** (VSA), ett **grupphanterat tjänst konto** (gMSA/SMSA) eller ett vanligt användar konto. De alternativ som stöds har ändrats med 2017 april-versionen av Connect när du gör en ny installation. Om du uppgraderar från en tidigare version av Azure AD Connect är dessa ytterligare alternativ inte tillgängliga.

| Typ av konto | Installations alternativ | Beskrivning |
| --- | --- | --- |
| [Virtuellt tjänst konto](#virtual-service-account) | Express och anpassad, 2017 april och senare | Detta är det alternativ som används för alla Express installationer, förutom för installationer på en domänkontrollant. För anpassad är det standard alternativet om inte ett annat alternativ används. |
| [Grupphanterat tjänstkonto](#group-managed-service-account) | Anpassad, 2017 april och senare | Om du använder en fjärran sluten SQL Server rekommenderar vi att du använder ett grupphanterat tjänst konto. |
| [Användar konto](#user-account) | Express och anpassad, 2017 april och senare | Ett användar konto som har prefixet AAD_ skapas bara under installationen när det installeras på Windows Server 2008 och när det installeras på en domänkontrollant. |
| [Användar konto](#user-account) | Express och anpassad, 2017 mars och tidigare | Ett lokalt konto som har prefixet AAD_ skapas under installationen. När du använder anpassad installation kan du ange ett annat konto. |

Om du använder Connect med en version från 2017 mars eller tidigare bör du inte återställa lösen ordet för tjänst kontot eftersom Windows förstör krypterings nycklarna av säkerhets skäl. Du kan inte ändra kontot till något annat konto utan att installera om Azure AD Connect. Om du uppgraderar till en version från 2017 april eller senare, stöds det att ändra lösen ordet för tjänst kontot, men du kan inte ändra det konto som används.

> [!Important]
> Du kan bara ange tjänst kontot vid första installationen. Det finns inte stöd för att ändra tjänst kontot när installationen har slutförts.

Det här är en tabell med alternativen standard, rekommenderas och stöds för kontot Sync-tjänst.

Förklaring:

- **Fet** anger standard alternativet och i de flesta fall rekommenderas det rekommenderade alternativet.
- *Kursiv* anger det rekommenderade alternativet när det inte är standard alternativet.
- 2008 – Standard alternativ vid installation på Windows Server 2008
- Alternativ som inte stöds av fet stil
- Lokalt konto – lokalt användar konto på servern
- Domän konto – domän användar konto
- sMSA- [fristående hanterat tjänst konto](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA- [grupphanterat tjänst konto](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Anpassad | Fjärr-SQL</br>Anpassad |
| --- | --- | --- | --- |
| **domänansluten dator** | **ATTRIBUTET**</br>Lokalt konto (2008) | **ATTRIBUTET**</br>Lokalt konto (2008)</br>Lokalt konto</br>Domänkonto</br>sMSA, gMSA | **gMSA**</br>Domänkonto |
| **Domänkontrollant** | **Domänkonto** | *gMSA*</br>**Domänkonto**</br>sMSA| *gMSA*</br>**Domänkonto**|

#### <a name="virtual-service-account"></a>Virtuellt tjänst konto
Ett virtuellt tjänst konto är en särskild typ av konto som inte har ett lösen ord och som hanteras av Windows.

![Skärm bild som visar det virtuella tjänst kontot (VSA).](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

VSA är avsedd att användas med scenarier där Synkroniseringsmotorn och SQL finns på samma server. Om du använder fjärr-SQL rekommenderar vi att du använder ett grupphanterat tjänst konto i stället.

Den här funktionen kräver Windows Server 2008 R2 eller senare. Om du installerar Azure AD Connect på Windows Server 2008 återgår installationen till att använda ett [användar konto](#user-account) i stället.

#### <a name="group-managed-service-account"></a>Grupphanterat tjänst konto
Om du använder en fjärran sluten SQL Server rekommenderar vi att du använder ett **grupphanterat tjänst konto**. Mer information om hur du förbereder din Active Directory för grupphanterade tjänst konton finns i [Översikt över grupphanterade tjänst konton](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).

Om du vill använda det här alternativet väljer du **Använd ett befintligt tjänst konto** på sidan [installera nödvändiga komponenter](how-to-connect-install-custom.md#install-required-components) och väljer **hanterat tjänst konto**.  
![ATTRIBUTET](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Det finns också stöd för att använda ett [fristående hanterat tjänst konto](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10)). Dessa kan dock endast användas på den lokala datorn och det finns ingen förmån att använda dem över standard kontot för den virtuella tjänsten.

Den här funktionen kräver Windows Server 2012 eller senare. Om du behöver använda ett äldre operativ system och använda fjärr-SQL måste du använda ett [användar konto](#user-account).

#### <a name="user-account"></a>Användar konto
Ett lokalt tjänst konto skapas av installations guiden (om du inte anger vilket konto som ska användas i anpassade inställningar). Kontot har prefix **AAD_** och används för den faktiska synkroniseringstjänsten som ska köras som. Om du installerar Azure AD Connect på en domänkontrollant skapas kontot i domänen. **AAD_** tjänst kontot måste finnas i domänen om:
   - du använder en fjärran sluten server som kör SQL Server
   - du använder en proxy som kräver autentisering

![Sync Service-konto](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Kontot skapas med ett långt komplext lösen ord som inte upphör att gälla.

Det här kontot används för att lagra lösen orden för de andra kontona på ett säkert sätt. De andra konto lösen orden lagras krypterade i-databasen. De privata nycklarna för krypterings nycklarna skyddas med kryptering av kryptografiska tjänsters hemliga nycklar med hjälp av Windows Data Protection API (DPAPI).

Om du använder en fullständig SQL Server är tjänst kontot DBO för den skapade databasen för Synkroniseringsmotorn. Tjänsten fungerar inte som avsedd för andra behörigheter. En SQL-inloggning skapas också.

Kontot beviljas också behörigheter till filer, register nycklar och andra objekt som är relaterade till Synkroniseringsmotorn.

### <a name="azure-ad-connector-account"></a>Azure AD Connector-konto
Ett konto i Azure AD skapas för Sync-tjänstens användning. Detta konto kan identifieras med dess visnings namn.

![Skärm bild som visar Azure AD-kontot.](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Namnet på den server som kontot används på kan identifieras i den andra delen av användar namnet. I bilden är Server namnet DC1. Om du har mellanlagrings servrar har varje server ett eget konto.

Kontot skapas med ett långt komplext lösen ord som inte upphör att gälla. Den beviljas ett särskilt konto för **synkronisering** av roll katalog som bara har behörighet att utföra synkronisering av aktiviteter. Den här särskilda inbyggda rollen kan inte beviljas utanför Azure AD Connects guiden. Azure Portal visar det här kontot med rollen **användare**.

Det finns en gräns på 20 Sync service-konton i Azure AD. Om du vill hämta en lista över befintliga Azure AD-tjänstekonton i din Azure AD kör du följande Azure AD PowerShell-cmdlet: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Om du vill ta bort oanvända Azure AD-tjänstekonton kör du följande Azure AD PowerShell-cmdlet: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Innan du kan använda ovanstående PowerShell-kommandon måste du installera [Azure Active Directory PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module) och ansluta till din instans av Azure AD med [Connect-AzureAD](/powershell/module/azuread/connect-azuread)

Mer information om hur du hanterar eller återställer lösen ordet för Azure AD Connector-kontot finns i [hantera Azure AD Connect-kontot](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Relaterad dokumentation
Om du inte läst dokumentationen om [att integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)innehåller följande tabell länkar till närliggande ämnen.

|Avsnitt |Länk|  
| --- | --- |
|Ladda ned Azure AD Connect | [Ladda ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Installera med standardinställningar | [Snabbinstallation av Azure AD Connect](how-to-connect-install-express.md)|
|Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](./how-to-connect-install-custom.md)|
|Uppgradera från DirSync | [Uppgradera från Azure AD-synkroniseringsverktyget (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Efter installationen | [Verifiera installationen och tilldela licenser](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
