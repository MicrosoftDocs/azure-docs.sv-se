---
title: Spelbok för adressering av vanliga säkerhets krav
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Den här artikeln innehåller vanliga säkerhets krav och metod tips i Azure SQL Database och Azure SQL-hanterad instans.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 09/21/2020
ms.reviewer: ''
ms.openlocfilehash: c16764d1ce985755b6a3042873cc18b09b697bcf
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551619"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>Spelbok för att lösa vanliga säkerhets krav med Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här artikeln innehåller metod tips för hur du löser vanliga säkerhets krav. Alla krav gäller inte för alla miljöer och du bör kontakta din databas och ditt säkerhets team på vilka funktioner som ska implementeras.

## <a name="solving-common-security-requirements"></a>Lösa vanliga säkerhets krav

Det här dokumentet ger vägledning om hur du löser vanliga säkerhets krav för nya eller befintliga program med hjälp av Azure SQL Database och Azure SQL-hanterad instans. Det är ordnat efter säkerhets områden på hög nivå. Information om hur du åtgärdar vissa hot finns i avsnittet [vanliga säkerhetshot och potentiella](#common-security-threats-and-potential-mitigations) åtgärder. Även om några av de rekommendationer som presenteras gäller när du migrerar program från en lokal plats till Azure, är inte migrations scenarier fokus i det här dokumentet.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Azure SQL Database distributions erbjudanden som beskrivs i den här guiden

- [Azure SQL Database](./index.yml): [enkla databaser](single-database-overview.md) och [elastiska pooler](elastic-pool-overview.md) i [servrar](logical-servers.md)
- [Hanterad Azure SQL-instans](../managed-instance/sql-managed-instance-paas-overview.md)

### <a name="deployment-offers-not-covered-in-this-guide"></a>Distributions erbjudanden som inte beskrivs i den här guiden

- Azure Synapse Analytics
- Virtuella Azure SQL-datorer (IaaS)
- SQL Server

### <a name="audience"></a>Målgrupp

De avsedda mål grupperna för den här guiden är kunder som har frågor om hur man skyddar Azure SQL Database. De roller som är intresserade av den här rekommenderade övnings artikeln är, men inte begränsat till:

- Security Architects
- Säkerhets chefer
- Ansvariga för efterlevnad
- Sekretess personal
- Säkerhets tekniker

### <a name="using-this-guide"></a><a id="using"></a> Använda den här guiden

Det här dokumentet är avsett som en assistent till vår befintliga [Azure SQL Database säkerhets](security-overview.md) dokumentation.

Om inget annat anges rekommenderar vi att du följer alla rekommenderade metoder som anges i varje avsnitt för att uppnå respektive mål eller krav. För att uppfylla särskilda standarder för säkerhetsefterlevnad eller bästa praxis, anges viktiga kontroll kontroller i avsnittet krav eller mål i förekommande fall. Det här är säkerhets standarder och föreskrifter som refereras till i det här dokumentet:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, sdl-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, kryptografi
- [Microsoft Operational Security Assurance (OSA)-metoder](https://www.microsoft.com/securityengineering/osa/practices): övning #1-6 och #9
- [NIST särskild publikation 800-53 säkerhets kontroller](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

Vi planerar att fortsätta att uppdatera rekommendationer och metod tips som anges här. Ange ininformation eller eventuella korrigeringar för det här dokumentet med hjälp av länken **feedback** längst ned i den här artikeln.

## <a name="authentication"></a>Autentisering

Autentisering är en process för att bevisa att användaren är den som han eller hon ansöker. Azure SQL Database-och SQL-hanterade instanser stöder två typer av autentisering:

- SQL-autentisering
- Azure Active Directory-autentisering

> [!NOTE]
> Azure Active Directory autentisering kanske inte stöds för alla verktyg och program från tredje part.

### <a name="central-management-for-identities"></a>Central hantering för identiteter

Central identitets hantering ger följande fördelar:

- Hantera grupp konton och kontrol lera användar behörigheter utan att duplicera inloggningar mellan servrar, databaser och hanterade instanser.
- Förenklad och flexibel behörighets hantering.
- Hantering av program i stor skala.

**Implementera**:

- Använd Azure Active Directory (Azure AD)-autentisering för centraliserad identitets hantering.

**Bästa praxis**:

- Skapa en Azure AD-klient och [skapa användare](../../active-directory/fundamentals/add-users-azure-active-directory.md) för att representera användare och skapa [tjänstens huvud namn](../../active-directory/develop/app-objects-and-service-principals.md) för att representera appar, tjänster och automatiserings verktyg. Tjänstens huvud namn motsvarar tjänst konton i Windows och Linux.

- Tilldela åtkomst behörigheter till resurser till Azure AD-huvudobjekt via grupp tilldelning: Skapa Azure AD-grupper, bevilja åtkomst till grupper och lägga till enskilda medlemmar i grupperna. I databasen skapar du inneslutna databas användare som mappar dina Azure AD-grupper. Om du vill tilldela behörigheter i databasen ska du placera de användare som är associerade med dina Azure AD-grupper i databas roller med rätt behörigheter.
  - Se artiklarna, [Konfigurera och hantera Azure Active Directory autentisering med SQL](authentication-aad-configure.md) och [Använd Azure AD för autentisering med SQL](authentication-aad-overview.md).
  > [!NOTE]
  > I SQL-hanterad instans kan du också skapa inloggningar som mappar till Azure AD-huvudobjekten i huvud databasen. Se [Skapa inloggning (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

- Att använda Azure AD-grupper fören klar behörighets hanteringen och både grupp ägaren och resurs ägaren kan lägga till/ta bort medlemmar till/från gruppen.

- Skapa en separat grupp för Azure AD-administratörer för varje server eller hanterad instans.

  - Se artikeln [etablera en Azure Active Directory administratör för servern](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

- Övervaka ändringar i Azure AD-gruppmedlemskapet med hjälp av Azure AD audits-rapporter.

- För en hanterad instans krävs ett separat steg för att skapa en Azure AD-administratör.
  - Se artikeln [etablera en Azure Active Directory administratör för din hanterade instans](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

> [!NOTE]
>
> - Azure AD-autentisering registreras i gransknings loggarna i Azure SQL, men inte i inloggnings loggarna för Azure AD.
> - Azure RBAC-behörigheter som beviljats i Azure gäller inte för Azure SQL Database-eller SQL-hanterade instans behörigheter. Sådana behörigheter måste skapas/mappas manuellt med befintliga SQL-behörigheter.
> - På klient sidan behöver Azure AD-autentisering till gång till Internet eller via en användardefinierad väg (UDR) till ett virtuellt nätverk.
> - Azure AD-åtkomsttoken cachelagras på klient sidan och dess livs längd beror på token-konfigurationen. Se artikeln, [konfigurerbara livstider för token i Azure Active Directory](../../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Råd om hur du felsöker problem med Azure AD-autentisering finns i följande blogg: [Felsöka Azure AD](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991).

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD-multifaktorautentisering

> Som nämns i: OSA-metoden #2, ISO Access Control (AC)

Azure AD Multi-Factor Authentication ger ytterligare säkerhet genom att kräva mer än en form av autentisering.

**Implementera**:

- [Aktivera Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) i Azure AD med villkorlig åtkomst och Använd interaktiv autentisering.

- Alternativet är att aktivera Multi-Factor Authentication för hela Azure AD-eller AD-domänen.

**Bästa praxis**:

- Aktivera villkorlig åtkomst i Azure AD (kräver Premium-prenumeration).
  - Se artikeln [villkorlig åtkomst i Azure AD](../../active-directory/conditional-access/overview.md).  

- Skapa en Azure AD-grupp (er) och aktivera Multi-Factor Authentication princip för valda grupper med hjälp av villkorlig åtkomst för Azure AD.
  - Se artikeln [Planera distribution av villkorlig åtkomst](../../active-directory/conditional-access/plan-conditional-access.md).

- Multi-Factor Authentication kan aktive ras för hela Azure AD eller för hela Active Directory federerade med Azure AD.

- Använd Azure AD Interactive Authentication mode för Azure SQL Database och Azure SQL-hanterad instans där ett lösen ord begärs interaktivt, följt av Multi-Factor Authentication:
  - Använd Universal Authentication i SSMS. Se artikeln [med Multi-Factor Azure AD-autentisering med Azure SQL Database, SQL-hanterad instans, Azure Synapse (SSMS-stöd för Multi-Factor Authentication)](authentication-mfa-ssms-overview.md).
  - Använd interaktiv autentisering som stöds i SQL Server Data Tools (SSDT). Se artikeln [Azure Active Directory support i SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?view=azuresqldb-current&preserve-view=true).
  - Använd andra SQL-verktyg som stöder Multi-Factor Authentication.
    - SSMS guide stöd för export/extrahera/distribuera databas  
    - [sqlpackage.exe](/sql/tools/sqlpackage): alternativ '/UA '
    - [SQLCMD-verktyg](/sql/tools/sqlcmd-utility): alternativ-G (interaktiv)
    - [BCP-verktyg](/sql/tools/bcp-utility): alternativ-G (interaktiv)

- Implementera dina program för att ansluta till Azure SQL Database eller Azure SQL-hanterad instans med interaktiv autentisering med stöd för Multi-Factor Authentication.
  - Se artikeln [Anslut till Azure SQL Database med Azure AD Multi-Factor Authentication](active-directory-interactive-connect-azure-sql-db.md).
  > [!NOTE]
  > Detta autentiseringsläge kräver användarspecifika identiteter. I de fall där en betrodd identitets modell används som kringgår individuell Azure AD-användarautentisering (t. ex. genom att använda hanterad identitet för Azure-resurser) gäller inte Multi-Factor Authentication.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimera användningen av lösenordsbaserad autentisering för användare

> Som nämns i: OSA-metoden #4, ISO Access Control (AC)

Lösenordsbaserade autentiseringsmetoder är en svagare form av autentisering. Autentiseringsuppgifter kan komprometteras eller av misstag tas bort.

**Implementera**:

- Använd en Azure AD-integrerad autentisering som eliminerar användningen av lösen ord.

**Bästa praxis**:

- Använd autentisering med enkel inloggning med Windows-autentiseringsuppgifter. Federera den lokala AD-domänen med Azure AD och Använd integrerad Windows-autentisering (för domänanslutna datorer med Azure AD).
  - Se artikeln SSMS- [stöd för Azure AD-integrerad autentisering](authentication-aad-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimera användningen av lösenordsbaserad autentisering för program

> Som nämns i: OSA-metoden #4, ISO Access Control (AC)

**Implementera**:

- Aktivera Azure-hanterad identitet. Du kan också använda integrerad eller certifikatbaserad autentisering.

**Bästa praxis**:

- Använd [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).
  - [Systemtilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [Användartilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Använda Azure SQL Database från Azure App Service med hanterad identitet (utan kod ändringar)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Använd cert-baserad autentisering för ett program.
  - Se det här [kod exemplet](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token).

- Använd Azure AD-autentisering för integrerad federerad domän och domänansluten dator (se avsnittet ovan).
  - Se [exempel programmet för integrerad autentisering](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Skydda lösen ord och hemligheter

Om det inte går att undvika lösen ord, se till att de är skyddade.

**Implementera**:

- Använd Azure Key Vault för att lagra lösen ord och hemligheter. Använd Multi-Factor Authentication för Azure SQL Database med Azure AD-användare när det är tillämpligt.

**Bästa praxis**:

- Om du inte kan undvika lösen ord eller hemligheter kan du lagra användar lösen ord och program hemligheter i Azure Key Vault och hantera åtkomst via Key Vault åtkomst principer.

- Olika ramverk för program utveckling kan också erbjuda branschspecifika mekanismer för att skydda hemligheter i appen. Till exempel: [ASP.net Core app](/aspnet/core/security/app-secrets?tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Använda SQL-autentisering för äldre program

SQL-autentisering syftar på autentiseringen av en användare vid anslutning till Azure SQL Database eller SQL-hanterad instans med hjälp av användar namn och lösen ord. En inloggning måste skapas på varje server eller hanterad instans och en användare som skapats i varje databas.

**Implementera**:

- Använd SQL-autentisering.

**Bästa praxis**:

- Skapa inloggningar och användare som en server eller instans administratör. Om du inte använder inneslutna databas användare med lösen ord lagras alla lösen ord i Master-databasen.
  - Se artikeln, [styra och beviljar databas åtkomst till SQL Database, SQL-hanterad instans och Azure Synapse Analytics](logins-create-manage.md).

## <a name="access-management"></a>Åtkomsthantering

Åtkomst hantering (kallas även auktorisering) är en process för att kontrol lera och hantera behöriga användares åtkomst och behörighet till Azure SQL Database eller SQL-hanterad instans.

### <a name="implement-principle-of-least-privilege"></a>Implementera princip för minsta behörighet

> Som nämns i: FedRamp-kontroller AC-06, NIST: AC-6, OSA-praxis #3

Principen om minsta behörighet anger att användarna inte bör ha fler privilegier än vad som behövs för att slutföra sina uppgifter. Mer information finns i artikeln [endast tillräckligt med administration](/powershell/scripting/learn/remoting/jea/overview).

**Implementera**:

Tilldela endast de [behörigheter](/sql/relational-databases/security/permissions-database-engine) som krävs för att slutföra de uppgifter som krävs:

- I SQL-databaser:
  - Använd detaljerad behörighet och användardefinierade databas roller (eller Server roller i en hanterad instans):
    1. Skapa de nödvändiga rollerna
       - [SKAPA ROLL](/sql/t-sql/statements/create-role-transact-sql)
       - [SKAPA SERVER ROLL](/sql/t-sql/statements/create-server-role-transact-sql)
    1. Skapa nödvändiga användare
       - [SKAPA ANVÄNDARE](/sql/t-sql/statements/create-user-transact-sql)
    1. Lägg till användare som medlemmar i roller
       - [ÄNDRA ROLL](/sql/t-sql/statements/alter-role-transact-sql)
       - [ÄNDRA SERVER ROLL](/sql/t-sql/statements/alter-server-role-transact-sql)
    1. Tilldela sedan behörigheter till roller.
       - [GRANT](/sql/t-sql/statements/grant-transact-sql)
  - Se till att inte tilldela användare till onödiga roller.

- I Azure Resource Manager:
  - Använd inbyggda roller om tillgängliga eller Azure-anpassade roller och tilldela de behörigheter som krävs.
    - [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md)
    - [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md)

**Bästa praxis**:

Följande metod tips är valfria men ger bättre hanterbarhet och support för din säkerhets strategi:

- Om möjligt börjar du med den minst möjliga uppsättningen behörigheter och börjar lägga till behörigheter en i taget, om det finns ett verkligt behov (och en motivering) – i stället för det motsatta tillvägagångs sättet: du tar bort behörigheter steg för steg.

- Avstå från att tilldela behörigheter till enskilda användare. Använd roller (databas-eller Server roller) konsekvent i stället. Roller hjälper till att kraftigt rapportera och felsöka behörigheter. (Azure RBAC stöder bara behörighets tilldelning via roller.)

- Skapa och Använd anpassade roller med de exakta behörigheter som krävs. Vanliga roller som används i praktiken:
  - Säkerhets distribution
  - Administratör
  - Utvecklare
  - Support personal
  - Granskare
  - Automatiserade processer
  - Slutanvändare
  
- Använd inbyggda roller endast när behörigheterna för rollerna matchar exakt de behörigheter som krävs för användaren. Du kan tilldela användare till flera roller.

- Kom ihåg att behörigheter i databas motorn kan tillämpas i följande omfattningar (den mindre omfattningen, vilket minskar effekten av beviljade behörigheter):
  - Server (särskilda roller i huvud databasen) i Azure
  - Databas
  - Schema
    - Vi rekommenderar att du använder scheman för att bevilja behörigheter i en databas. (se även: [schema design: rekommendationer för schema design med säkerhet i åtanke](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Objekt (tabell, vy, procedur osv.)

  > [!NOTE]
  > Vi rekommenderar inte att du tillämpar behörigheter på objekt nivån eftersom den här nivån lägger till onödig komplexitet i den övergripande implementeringen. Om du bestämmer dig för att använda behörigheter på objekt nivå bör de dokumenteras tydligt. Detsamma gäller för kolumn nivå behörigheter, vilket är ännu mindre rekommenderat av samma skäl. Tänk också på att som standard är en åsidosättning på [tabell nivå inte](/sql/t-sql/statements/deny-object-permissions-transact-sql) åsidosätter ett bidrag på kolumn nivå. Detta kräver att [konfigurationen av kompabilitets Server för gemensamma villkor](/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) aktive ras.

- Utför regelbundna kontroller med hjälp av [sårbarhets bedömning (va)](/sql/relational-databases/security/sql-vulnerability-assessment) för att testa för många behörigheter.

### <a name="implement-separation-of-duties"></a>Implementera separering av uppgifter

> Nämns i: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Separering av uppgifter, även kallat ansvars fördelning, beskriver kravet på att dela upp känsliga uppgifter i flera uppgifter som tilldelas till olika användare. Separering av uppgifter förhindrar data intrång.

**Implementera**:

- Identifiera den nivå som krävs för att åtskilja uppgifter. Exempel:
  - Mellan utvecklings-/test-och produktions miljöer
  - Säkerhets känsliga uppgifter och hanterings nivåer för databas administratörer (DBA) och uppgifter i utvecklare.
    - Exempel: granskare, skapa säkerhets principer för säkerhet på roll nivå (RLS), implementera SQL Database objekt med DDL-behörigheter.

- Identifiera en omfattande hierarki med användare (och automatiserade processer) som har åtkomst till systemet.

- Skapa roller enligt de användar grupper som behövs och tilldela roller behörigheter.
  - För aktiviteter på hanterings nivå i Azure Portal eller via PowerShell-Automation använder du Azure-roller. Hitta antingen en inbyggd roll som matchar kravet eller skapa en anpassad Azure-roll med hjälp av de tillgängliga behörigheterna
  - Skapa Server roller för Server-wide-aktiviteter (skapa nya inloggningar, databaser) i en hanterad instans.
  - Skapa databas roller för aktiviteter på databas nivå.

- För vissa känsliga uppgifter bör du överväga att skapa särskilda lagrade procedurer som signerats av ett certifikat för att köra aktiviteter för användarens räkning. En viktig fördel med digitalt signerade lagrade procedurer är att om proceduren ändras tas behörigheter som har beviljats för den tidigare versionen av proceduren omedelbart bort.
  - Exempel: [självstudie: signera lagrade procedurer med ett certifikat](/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- Implementera transparent datakryptering (TDE) med Kundhanterade nycklar i Azure Key Vault för att möjliggöra separering av uppgifter mellan data ägare och säkerhets ägare.
  - Se artikeln [Konfigurera Kundhanterade nycklar för Azure Storage kryptering från Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md).

- För att säkerställa att en DBA inte kan se data som anses hög känslig och fortfarande kan utföra DBA-uppgifter kan du använda Always Encrypted med separering av roller.
  - Se artiklarna, [Översikt över nyckel hantering för Always Encrypted](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [nyckel etablering med](/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)separering av roller och [kolumn huvud nyckel rotation med separering av roller](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation).

- I de fall då användningen av Always Encrypted inte är genomförbar, eller åtminstone inte utan större kostnader och ansträngningar som kan leda till att systemet är nära oanvändbart, kan kompromisser göras och minskas genom användning av kompensations kontroller som:
  - Mänsklig inblandning i processer.
  - Gransknings historik – mer information om granskning finns i, [Granska kritiska säkerhets händelser](#audit-critical-security-events).

**Bästa praxis**:

- Se till att olika konton används för utvecklings-/test-och produktions miljöer. Olika konton hjälper till att uppfylla separationen av test-och produktions system.

- Avstå från att tilldela behörigheter till enskilda användare. Använd roller (databas-eller Server roller) konsekvent i stället. Att ha roller hjälper till att kraftigt hantera rapporterings-och fel söknings behörigheter.

- Använd inbyggda roller när behörigheterna matchar exakt de behörigheter som krävs – om union av alla behörigheter från flera inbyggda roller leder till en 100%-matchning, kan du även tilldela flera roller samtidigt.

- Skapa och Använd användardefinierade roller när inbyggda roller beviljar för många behörigheter eller otillräcklig behörighet.

- Roll tilldelningar kan också utföras tillfälligt, även kallat dynamisk avgränsning av uppgifter (DSD), antingen inom SQL Agent Job-stegen i T-SQL eller med Azure PIM för Azure-roller.

- Kontrol lera att databas administratörer inte har åtkomst till krypterings nycklar eller nyckel lager och att säkerhets administratörer med åtkomst till nycklarna inte har åtkomst till databasen i sin tur. Användningen av [EKM (Extensible Key Management)](/sql/relational-databases/security/encryption/extensible-key-management-ekm) kan göra den här separationen lättare att uppnå. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kan användas för att implementera EKM.

- Se alltid till att ha en Gransknings logg för säkerhetsrelaterade åtgärder.

- Du kan hämta definitionen av de inbyggda Azure-rollerna för att se de behörigheter som används och skapa en anpassad roll baserat på utdrag och kumulation av dessa via PowerShell.

- Eftersom en medlem i db_owner databas rollen kan ändra säkerhets inställningar som transparent datakryptering (TDE) eller ändra service nivå mål, bör detta medlemskap beviljas med försiktighet. Det finns dock många aktiviteter som kräver db_owner-behörighet. Uppgift som att ändra en databas inställning, till exempel ändra DB-alternativ. Granskningen spelar en nyckel roll i vilken lösning som helst.

- Det går inte att begränsa behörigheterna för en db_owner och därför förhindra ett administrativt konto från att Visa användar data. Om det finns mycket känsliga data i en databas kan Always Encrypted användas för att på ett säkert sätt förhindra db_owners eller andra DBA att visa den.

> [!NOTE]
> Att uppnå separering av uppgifter (SoD) är en utmaning för säkerhetsrelaterade eller fel söknings uppgifter. Andra områden som utvecklings-och slut användar roller är enklare att åtskilja. De mest kompatibla kontrollerna tillåter användning av alternativa kontroll funktioner som granskning när andra lösningar inte är praktiska.

För läsarna som vill gå djupare till SoD rekommenderar vi följande resurser:

- För Azure SQL Database-och SQL-hanterad instans:  
  - [Kontrollera och att bevilja åtkomst till databasen](logins-create-manage.md)
  - [Motor separering av uppgifter för programutvecklaren](/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [Separering av uppgifter](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Signering av lagrade procedurer](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- För Azure-resurs hantering:
  - [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md)
  - [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md)
  - [Använda Azure AD Privileged Identity Management för utökad åtkomst](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Utföra regelbunden kod granskning

> Nämnt i: PCI: 6.3.2, SOC: SDL-3

Separering av uppgifter är inte begränsat till data i en databas, men innehåller program kod. Skadlig kod kan eventuellt kringgå säkerhets kontroller. Innan du distribuerar anpassad kod till produktion är det viktigt att granska vad som distribueras.

**Implementera**:

- Använd ett databas verktyg som Azure Data Studio som har stöd för käll kontroll.

- Implementera en distributions process med åtskiljande kod.

- Innan du genomför till huvud grenen, måste en person (förutom själva författaren av själva koden) kontrol lera koden för potentiell höjning av privilegierade risker och skadliga data ändringar för att skydda mot bedrägerier och falsk åtkomst. Detta kan göras med hjälp av mekanismer för käll kontroll.

**Bästa praxis**:

- Standardisering: den hjälper till att implementera en standard procedur som ska följas för kod uppdateringar.

- Sårbarhets bedömning innehåller regler som kontrollerar alltför stora behörigheter, användning av gamla krypteringsalgoritmer och andra säkerhets problem i ett databas schema.

- Ytterligare kontroller kan göras i en frågor och svars miljö med hjälp av avancerat skydd som söker efter kod som är sårbar för SQL-injektering.

- Exempel på vad du kan se för:
  - Skapa en användare eller ändra säkerhets inställningar inifrån en automatiserad SQL-Code-Update-distribution.
  - En lagrad procedur, som, beroende på vilka parametrar som anges, uppdaterar ett monetärt värde i en cell i ett icke-förformat sätt.

- Se till att personen som genomför granskningen är en annan person än den ursprungliga kod författaren och kunskaps bara vid kod granskning och säker kodning.

- Se till att du känner till alla källor med kod ändringar. Koden kan vara i T-SQL-skript. Det kan vara ad hoc-kommandon som ska köras eller distribueras i form av vyer, funktioner, utlösare och lagrade procedurer. Det kan vara en del av jobb definitionerna för SQL-agenten (steg). Det kan också köras inifrån SSIS-paket, Azure Data Factory och andra tjänster.

## <a name="data-protection"></a>Dataskydd

Data skydd är en uppsättning funktioner för att skydda viktig information från kompromisser med kryptering eller döljande.

> [!NOTE]
> Microsoft intygar till Azure SQL Database och SQL-hanterad instans som FIPS 140-2 nivå 1-kompatibel. Detta görs när du har verifierat den strikta användningen av FIPS 140-2-nivå 1-acceptabla algoritmer och FIPS 140-2 nivå 1-validerade instanser av algoritmerna, inklusive konsekvens med nödvändiga nyckel längder, nyckel hantering, nyckel generering och nyckel lagring. Denna attestering är avsedd att göra det möjligt för våra kunder att svara på behovet eller kravet på att använda FIPS 140-2 nivå 1-verifierade instanser i data bearbetningen eller leverans av system eller program. Vi definierar villkoren "FIPS 140-2 nivå 1-kompatibel" och "FIPS 140-2 Level 1-kompatibilitet" som används i ovanstående instruktion för att påvisa den avsedda tillämpligheten för USA och Kanadas myndighets användning av den olika termen "FIPS 140-2 Level 1".

### <a name="encrypt-data-in-transit"></a>Kryptera data under överföring

> Som nämns i: OSA-praxis #6, ISO-kontroll Family: kryptografi

Skyddar dina data medan data flyttas mellan klienten och servern. Se [nätverks säkerhet](#network-security).

### <a name="encrypt-data-at-rest"></a>Kryptera data i vila

> Som nämns i: OSA-praxis #6, ISO-kontroll Family: kryptografi

Kryptering i vila är ett kryptografiskt skydd av data när de sparas i databas-, logg-och säkerhets kopior.

**Implementera**:

- [Transparent databas kryptering (TDE)](transparent-data-encryption-tde-overview.md) med tjänst hanterade nycklar är aktiverade som standard för alla databaser som skapats efter 2017 i Azure SQL Database-och SQL-hanterad instans.
- I en hanterad instans, om databasen skapas från en återställnings åtgärd med en lokal server, kommer TDE-inställningen för den ursprungliga databasen att följas. Om den ursprungliga databasen inte har TDE aktiverat, rekommenderar vi att TDE aktive ras manuellt för den hanterade instansen.

**Bästa praxis**:

- Lagra inte data som kräver kryptering vid vila i huvud databasen. Huvud databasen kan inte krypteras med TDE.

- Använd Kundhanterade nycklar i Azure Key Vault om du behöver större insyn och detaljerad kontroll över TDE-skyddet. Azure Key Vault ger möjlighet att återkalla behörigheter när som helst för att rendera databasen otillgänglig. Du kan centralt hantera TDE-skydd tillsammans med andra nycklar, eller rotera TDE-skyddet enligt ditt eget schema med Azure Key Vault.

- Om du använder Kundhanterade nycklar i Azure Key Vault följer du artiklarna, [rikt linjerna för att konfigurera TDE med Azure Key Vault](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv) och [hur du konfigurerar geo-Dr med Azure Key Vault](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Skydda känsliga data som används från privilegierade användare med hög behörighet

Data som används är de data som lagras i minnet för databas systemet under körningen av SQL-frågor. Om din databas lagrar känsliga data kan din organisation krävas för att se till att privilegierade användare hindras från att Visa känsliga data i databasen. Användare med hög behörighet, till exempel Microsoft-operatörer eller databas administratörer i din organisation, bör kunna hantera databasen, men förhindras från att visa och potentiellt organisationers känsliga data från SQL-processens minne eller genom att fråga databasen.

De principer som avgör vilka data som är känsliga och om känsliga data måste krypteras i minnet och inte är tillgängliga för administratörer i klartext, är de som är speciella för din organisation och de regler för efterlevnad som du måste följa. Se det relaterade kravet: [identifiera och tagga känsliga data](#identify-and-tag-sensitive-data).

**Implementera**:

- Använd [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) för att säkerställa att känsliga data inte visas i klartext i Azure SQL Database eller SQL-hanterad instans, även i minnes-/användnings läge. Always Encrypted skyddar data från databas administratörer (databas administratörer) och moln administratörer (eller dåliga aktörer som kan personifiera hög privilegierade och obehöriga användare) och ger dig större kontroll över vem som har åtkomst till dina data.

**Bästa praxis**:

- Always Encrypted är inte en ersättning för att kryptera data i vila (TDE) eller under överföring (SSL/TLS). Always Encrypted bör inte användas för icke-känsliga data för att minimera prestanda-och funktions påverkan. Att använda Always Encrypted tillsammans med TDE och Transport Layer Security (TLS) rekommenderas för omfattande skydd av data i vila, under överföring och används.

- Bedöm effekten av att kryptera de identifierade känsliga data kolumnerna innan du distribuerar Always Encrypted i en produktions data bas. I allmänhet minskar Always Encrypted funktionen för frågor i krypterade kolumner och har andra begränsningar, som visas i [Always Encrypted-funktions information](/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Därför kan du behöva återskapa ditt program för att kunna implementera funktionerna igen, en fråga stöder inte, på klient sidan eller/och återkallas ditt databas schema, inklusive definitioner av lagrade procedurer, funktioner, vyer och utlösare. Befintliga program kanske inte fungerar med krypterade kolumner om de inte följer begränsningar och begränsningar för Always Encrypted. Även om eko systemet för Microsoft-verktyg, produkter och tjänster som stöder Always Encrypted växer, fungerar inte ett antal dem med krypterade kolumner. Att kryptera en kolumn kan också påverka prestanda för frågor, beroende på arbets Belastningens egenskaper.

- Hantera Always Encrypted nycklar med rollseparering om du använder Always Encrypted för att skydda data från skadliga databas administratörer. Med rollseparering skapas fysiska nycklar av en säkerhets administratör. DBA skapar kolumn huvud nyckeln och kolumn krypterings nyckelns metadata objekt som beskriver de fysiska nycklarna i databasen. Under den här processen behöver inte säkerhets administratören åtkomst till databasen och DBA behöver inte åtkomst till de fysiska nycklarna i klartext.
  - Mer information finns i artikeln [Hantera nycklar med separering av roller](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) .

- Lagra dina kolumn huvud nycklar i Azure Key Vault för enkel hantering. Undvik att använda Windows certifikat arkiv (och i allmänhet distribuerade nyckel lagrings lösningar, i motsats till centrala nyckel hanterings lösningar) som gör nyckel hanteringen hårt.

- Tänk noggrant igenom nack delarna med att använda flera nycklar (kolumn huvud nyckel eller kolumn krypterings nycklar). Behåll antalet nycklar små för att minska kostnaderna för nyckel hantering. En kolumn huvud nyckel och en kolumn krypterings nyckel per databas är vanligt vis tillräckligt i stabilt läges miljöer (inte i mitten av en nyckel rotation). Du kan behöva ytterligare nycklar om du har olika användar grupper, var och en använder olika nycklar och åtkomst till olika data.  

- Rotera kolumn huvud nycklar enligt dina krav. Om du också behöver rotera kolumn krypterings nycklar bör du överväga att använda online-kryptering för att minimera avbrotts tiden för programmet.
  - Se överväganden för artikel, [prestanda och tillgänglighet](/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations).

- Använd deterministisk kryptering om beräkningarna (jämlikhet) för data behöver stödjas. Annars använder du slumpmässig kryptering. Undvik att använda deterministisk kryptering för data uppsättningar med låg entropi eller data uppsättningar med offentligt känd distribution.

- Om du är bekymrad om att tredje parter kommer åt dina data lagligt utan ditt medgivande, se till att alla program och verktyg som har åtkomst till nycklar och data i klartext körs utanför Microsoft Azure molnet. Utan åtkomst till nycklarna har den tredje parten inget sätt att dekryptera data om inte krypteringen kringgås.

- Always Encrypted har inte enkelt stöd för att bevilja tillfällig åtkomst till nycklarna (och skyddade data). Om du till exempel behöver dela nycklarna med en DBA så att DBA kan utföra vissa rengörings åtgärder på känsliga och krypterade data. Det enda sättet att använda tillförlitligheten att återkalla åtkomsten till data från DBA är att rotera både kolumn krypterings nycklarna och kolumn huvud nycklarna som skyddar data, vilket är en dyr åtgärd.

- För att få åtkomst till klartext-värden i krypterade kolumner måste användaren ha åtkomst till den kolumn huvud nyckel (CMK) som skyddar kolumner, som har kon figurer ATS i nyckel lagret som innehåller CMK. Användaren måste också ha en **kolumn huvud nyckel definition** och **Visa alla kolumn krypterings nyckel definitioner** databas behörigheter.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Kontrol lera åtkomsten till program användare till känsliga data via kryptering

Kryptering kan användas som ett sätt för att säkerställa att endast specifika program användare som har åtkomst till kryptografiska nycklar kan visa eller uppdatera data.

**Implementera**:

- Använd kryptering på cell nivå (CLE). Mer information finns i artikeln [kryptera en data kolumn](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) .
- Använd Always Encrypted, men var medveten om dess begränsning. Begränsningarna visas nedan.

**Bästa praxis**

När du använder CLE:

- Kontrol lera åtkomst till nycklar via SQL-behörigheter och-roller.

- Använd AES (AES 256 rekommenderas) för data kryptering. Algoritmer, t. ex. RC4, DES och TripleDES, är inaktuella och bör inte användas på grund av kända sårbarheter.

- Skydda symmetriska nycklar med asymmetriska nycklar/certifikat (inte lösen ord) för att undvika att använda 3DES.

- Var försiktig när du migrerar en databas med Cell-Level kryptering via export/import (BACPAC-filer).
  - Se artikeln [rekommendationer för att använda kryptering på cell nivå i Azure SQL Database](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database) om hur du förhindrar att nycklar går förlorade vid migrering av data, och för andra rekommendationer om bästa praxis.

Tänk på att Always Encrypted främst har utformats för att skydda känsliga data som används från användare med hög behörighet för Azure SQL Database (moln operatörer, databas administratörer) – se [skydda känsliga data som används från privilegierade användare med hög privilegier](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Tänk på följande utmaningar när du använder Always Encrypted för att skydda data från program användare:

- Som standard har alla driv rutiner för Microsoft-klienter som stöder Always Encrypted en global (ett per program) cache med kolumn krypterings nycklar. När en klient driv rutin hämtar en kolumn krypterings nyckel i klartext genom att kontakta en nyckel lagrings plats som innehåller en kolumn huvud nyckel cachelagras kolumn krypterings nyckeln i klartext. Detta gör det svårare att isolera data från användare av program med flera användare. Om ditt program personifierar slutanvändare när de interagerar med ett nyckel lager (till exempel Azure Key Vault), efter att en användares fråga fyller i cachen med en kolumn krypterings nyckel, kommer en efterföljande fråga som kräver samma nyckel men som utlösts av en annan användare att använda den cachelagrade nyckeln. Driv rutinen anropar inte nyckel lagret och kontrollerar inte om den andra användaren har behörighet att komma åt kolumn krypterings nyckeln. Det innebär att användaren kan se krypterade data även om användaren inte har åtkomst till nycklarna. För att uppnå isolering av användare i ett program med flera användare kan du inaktivera cachelagring av kolumn krypterings nycklar. Inaktive ring av cachelagring leder till ytterligare prestanda omkostnader eftersom driv rutinen måste kontakta nyckel lagret för varje data kryptering eller dekryptering.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Skydda data mot obehörig visning av program användare samtidigt som du behåller data formatet

En annan metod för att förhindra obehöriga användare från att visa data är att obfuscate eller maskera data samtidigt som data typerna och formaten bevaras så att användar programmen kan fortsätta hantera och visa data.

**Implementera**:

- Använd [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking) för att obfuscate tabell kolumner.

> [!NOTE]
> Always Encrypted fungerar inte med dynamisk data maskning. Det går inte att kryptera och maskera samma kolumn, vilket innebär att du måste prioritera att skydda data som används jämfört med att maskera data för dina App-användare via dynamisk data maskning.

**Bästa praxis**:

> [!NOTE]
> Dynamisk data maskning kan inte användas för att skydda data från användare med hög behörighet. Maskering av principer gäller inte för användare med administrativ åtkomst som db_owner.

- Tillåt inte att App-användare kör ad hoc-frågor (eftersom de kan fungera kring dynamisk data maskning).  
  - Mer information finns i artikeln om att [kringgå maskning med hjälp av en metod för att maskera eller brutet kraft](/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) .  

- Använd en lämplig princip för åtkomst kontroll (via SQL-behörigheter, roller, RLS) för att begränsa användar behörigheter för att göra uppdateringar i de maskerade kolumnerna. Att skapa en mask i en kolumn förhindrar inte uppdateringar i den kolumnen. Användare som tar emot maskerade data när de frågar den maskerade kolumnen kan uppdatera data om de har Skriv behörighet.

- Dynamisk data maskning bevarar inte statistiska egenskaper för de maskerade värdena. Detta kan påverka frågeresultaten (till exempel frågor som innehåller filtrerings predikat eller kopplingar till maskerade data).

## <a name="network-security"></a>Nätverkssäkerhet

Nätverks säkerhet avser åtkomst kontroller och bästa metoder för att skydda dina data under överföring till Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>Konfigurera klienten för att ansluta säkert till SQL Database/SQL-hanterad instans

Bästa praxis för att förhindra klient datorer och program med välkända sårbarheter (till exempel att använda äldre TLS-protokoll och chiffersviter) från att ansluta till Azure SQL Database-och SQL-hanterad instans.

**Implementera**:

- Se till att klient datorer som ansluter till Azure SQL Database och SQL-hanterad instans använder  [Transport Layer Security (TLS)](security-overview.md#transport-layer-security-encryption-in-transit).

**Bästa praxis**:

- Konfigurera alla dina appar och verktyg för att ansluta till SQL Database med kryptering aktiverat
  - Kryptera = på, TrustServerCertificate = av (eller motsvarande driv rutiner som inte kommer från Microsoft).

- Om din app använder en driv rutin som inte stöder TLS eller som stöder en äldre version av TLS, ersätter du driv rutinen om det är möjligt. Om det inte är möjligt, utvärderar du säkerhets riskerna noggrant.

- Minska angrepps vektorer via sårbarheter i SSL 2,0, SSL 3,0, TLS 1,0 och TLS 1,1 genom att inaktivera dem på klient datorer som ansluter till Azure SQL Database per [Transport Layer Security (TLS) register inställningar](/windows-server/security/tls/tls-registry-settings#tls-10).

- Kontrol lera chiffersviter som är tillgängliga på klienten: [cipher-paket i TLS/SSL (Schannel SSP)](/windows/desktop/SecAuthN/cipher-suites-in-schannel). Mer specifikt är att inaktivera 3DES per [konfigurering av TLS cipher Suite-ordningen](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order).

- För Azure SQL Database och SQL-hanterad instans tillämpas kryptering för både proxy-och omdirigerings-anslutnings typer. För Azure SQL-hanterad instans använder du anslutnings typen **proxy** (standard) som detta tvingar kryptering från Server sidan. Anslutnings typen för **omdirigering** stöder för närvarande inte tvingande kryptering och är bara tillgänglig för privata IP-anslutningar.

- Mer information finns i [Azure SQL Database anslutnings arkitektur – anslutnings princip](connectivity-architecture.md#connection-policy).

### <a name="minimize-attack-surface"></a>Minimera attack ytan

Minimera antalet funktioner som kan angripas av en obehörig användare. Implementera nätverks åtkomst kontroller för Azure SQL Database.

> Nämns i: OSA-praxis #5

**Implementera**:

I SQL Database:

- Ange Tillåt åtkomst till Azure-tjänster på server nivå
- Använda VNet-tjänstens slut punkter och brand Väggs regler för VNet.
- Använd privat länk (förhands granskning).

I SQL-hanterad instans:

- Följ rikt linjerna i [nätverks kraven](../managed-instance/connectivity-architecture-overview.md#network-requirements).

**Bästa praxis**:

- Begränsa åtkomsten till Azure SQL Database-och SQL-hanterad instans genom att ansluta på en privat slut punkt (till exempel med en privat data Sök väg):
  - En hanterad instans kan isoleras i ett virtuellt nätverk för att förhindra extern åtkomst. Program och verktyg som finns i samma eller peer-kopplat virtuella nätverk i samma region kan komma åt det direkt. Program och verktyg som finns i olika regioner kan använda virtuell-nätverks-till-virtuell-nätverks anslutning eller ExpressRoute krets-peering för att upprätta anslutning. Kunden bör använda nätverks säkerhets grupper (NSG) för att begränsa åtkomsten över port 1433 till resurser som kräver åtkomst till en hanterad instans.
  - För en SQL Database använder du funktionen [privat länk](../../private-link/private-endpoint-overview.md) som tillhandahåller en dedikerad privat IP-adress för servern i det virtuella nätverket. Du kan också använda [tjänst slut punkter för virtuella nätverk med brand Väggs regler för virtuella nätverk](vnet-service-endpoint-rule-overview.md) för att begränsa åtkomsten till dina servrar.
  - Mobila användare bör använda punkt-till-plats-VPN-anslutningar för att ansluta över data Sök vägen.
  - Användare som är anslutna till sitt lokala nätverk bör använda plats-till-plats-VPN-anslutning eller ExpressRoute för att ansluta över data Sök vägen.

- Du kan komma åt Azure SQL Database-och SQL-hanterad instans genom att ansluta till en offentlig slut punkt (till exempel med hjälp av en offentlig data Sök väg). Följande metod tips bör övervägas:
  - För en server i SQL Database använder du [regler för IP-brandvägg](firewall-configure.md) för att begränsa åtkomsten till endast auktoriserade IP-adresser.
  - För SQL-hanterad instans använder du nätverks säkerhets grupper (NSG) för att begränsa åtkomsten över Port 3342 till nödvändiga resurser. Mer information finns i [använda en hanterad instans på ett säkert sätt med offentliga slut punkter](../managed-instance/public-endpoint-overview.md).

> [!NOTE]
> Den offentliga SQL Managed instance-slutpunkten är inte aktive rad som standard och måste aktive ras explicit. Om företags principen inte tillåter användning av offentliga slut punkter använder [Azure policy](../../governance/policy/overview.md) för att förhindra att offentliga slut punkter aktive ras på den första platsen.

- Konfigurera Azure-nätverks komponenter:
  - Följ [Azures metod tips för nätverks säkerhet](../../security/fundamentals/network-best-practices.md).
  - Planera Virtual Network konfiguration enligt bästa praxis som beskrivs i [Azure Virtual Network vanliga frågor och svar (FAQ)](../../virtual-network/virtual-networks-faq.md) och planera.
  - Segmentera ett virtuellt nätverk i flera undernät och tilldela resurser för liknande roll till samma undernät (till exempel frontend vs backend-resurser).
  - Använd [nätverks säkerhets grupper (NSG: er)](../../virtual-network/network-security-groups-overview.md) för att styra trafiken mellan undernät i Azure Virtual Network-gränser.
  - Aktivera [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) för din prenumeration för att övervaka inkommande och utgående nätverks trafik.

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>Konfigurera Power BI för säkra anslutningar till SQL Database/SQL-hanterad instans

**Bästa praxis**:

- För Power BI Desktop använder du en privat data Sök väg när det är möjligt.

- Se till att Power BI Desktop ansluter med TLS 1.2 genom att ange register nyckeln på klient datorn enligt [Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings) register inställningar.

- Begränsa data åtkomsten för vissa användare via [säkerhet på radnivå (RLS) med Power BI](/power-bi/service-admin-rls).

- För Power BI tjänst använder du den [lokala datagatewayen](/power-bi/service-gateway-onprem)och ska tänka på [begränsningar och överväganden](/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>Konfigurera App Service för säkra anslutningar till SQL Database/SQL-hanterad instans

**Bästa praxis**:

- För en enkel webbapp kräver anslutning via offentlig slut punkt att **Azure-tjänster** ställs in på.

- [Integrera din app med en Azure-Virtual Network](../../app-service/web-sites-integrate-with-vnet.md) för anslutning av privata data vägar till en hanterad instans. Alternativt kan du också distribuera en webbapp med [App Service miljöer (ASE)](../../app-service/environment/intro.md).

- För webbapp med ASE eller det virtuella nätverkets integrerade webbapp som ansluter till en databas i SQL Database kan du använda [tjänst slut punkter för virtuella nätverk och brand Väggs regler för virtuella](vnet-service-endpoint-rule-overview.md) nätverk för att begränsa åtkomsten från ett särskilt virtuellt nätverk och undernät. Ställ sedan in **Tillåt Azure-tjänster** på av. Du kan också ansluta ASE till en hanterad instans i SQL-hanterad instans via en privat data Sök väg.  

- Kontrol lera att din webbapp har kon figurer ATS per artikel, [metod tips för att skydda PaaS-webb program (Platform as a Service) och mobila program med hjälp av Azure App Service](../../security/fundamentals/paas-applications-using-app-services.md).

- Installera [brand vägg för webbaserade program (WAF)](../../web-application-firewall/ag/ag-overview.md) för att skydda din webbapp från vanliga sårbarheter och sårbarheter.

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>Konfigurera värd för virtuella Azure-datorer för säkra anslutningar till SQL Database/SQL-hanterad instans

**Bästa praxis**:

- Använd en kombination av reglerna för att tillåta och neka på NSG: er i Azure Virtual Machines för att styra vilka regioner som kan nås från den virtuella datorn.

- Se till att den virtuella datorn har kon figurer ATS enligt artikeln och [rekommenderade säkerhets metoder för IaaS-arbetsbelastningar i Azure](../../security/fundamentals/iaas.md).

- Se till att alla virtuella datorer är associerade med ett särskilt virtuellt nätverk och undernät.

- Utvärdera om du behöver standard vägen 0.0.0.0/Internet enligt rikt linjerna för [Tvingad tunnel trafik](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling).
  - Om ja – till exempel front-end-undernät, behåll standard vägen.
  - Om ingen – till exempel mellan nivåer eller backend-undernät – aktivera Tvingad tunnel trafik så att ingen trafik går via Internet för att komma åt lokalt (a. k. ett kors lokalt).

- Implementera [valfria standard vägar](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) om du använder peering eller ansluter till en lokal plats.

- Implementera [användardefinierade vägar](../../virtual-network/virtual-networks-udr-overview.md#user-defined) om du behöver skicka all trafik i det virtuella nätverket till en virtuell nätverks installation för paket granskning.

- Använd [tjänst slut punkter i virtuella nätverk](vnet-service-endpoint-rule-overview.md) för säker åtkomst till PaaS-tjänster som Azure Storage via Azure stamnät nätverket.

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Skydda mot DDoS-attacker (distributed denial of Service)

DDoS-attacker (distributed denial of Service) försöker av en obehörig användare att skicka en översvämning av nätverks trafik till Azure SQL Database i syfte att överbelasta Azure-infrastrukturen och göra det lättare att avvisa giltiga inloggningar och arbets belastningar.

> Nämns i: OSA-praxis #9

**Implementera**:

DDoS Protection aktive ras automatiskt som en del av Azure-plattformen. Det omfattar alltid trafik övervakning och real tids minskning av attacker på nätverks nivå på offentliga slut punkter.

- Använd [Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md) för att övervaka offentliga IP-adresser som är kopplade till resurser som har distribuerats i virtuella nätverk.

- Använd [Avancerat skydd för Azure SQL Database](threat-detection-overview.md) för att identifiera DOS-attacker (Denial of Service) mot databaser.

**Bästa praxis**:

- Följ de metoder som beskrivs i [minimera attack ytan](#minimize-attack-surface) för att minimera risken för DDoS-attacker.

- Varningen för att förhindra att **SQL-autentiseringsuppgifter är bruten** för att identifiera brute force-attacker. I vissa fall kan aviseringen även särskilja inträngande test arbets belastningar.

- För virtuella Azure-datorer som är värd för program som ansluter till SQL Database:
  - Följ rekommendationer för att begränsa åtkomst via Internet-riktade slut punkter i Azure Security Center.
  - Använd skalnings uppsättningar för virtuella datorer för att köra flera instanser av ditt program på virtuella Azure-datorer.
  - Inaktivera RDP och SSH från Internet för att förhindra brute force-attack.

## <a name="monitoring-logging-and-auditing"></a>Övervakning, loggning och granskning

Det här avsnittet handlar om funktioner som hjälper dig att identifiera avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Den beskriver också metod tips för att konfigurera databas granskning för att spåra och avbilda databas händelser.

### <a name="protect-databases-against-attacks"></a>Skydda databaser mot attacker

Med avancerat skydd kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar om avvikande aktiviteter.

**Implementera**:

- Använd [Avancerat skydd mot SQL för](threat-detection-overview.md#alerts) att identifiera ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser, inklusive:
  - SQL-inmatning-attack.
  - Stöld/läckage för autentiseringsuppgifter.
  - Missbruk av behörighet.
  - Data exfiltrering.

**Bästa praxis**:

- Konfigurera [Azure Defender för SQL](azure-defender-for-sql.md)   för en bestämd Server eller en hanterad instans. Du kan också konfigurera Azure Defender för SQL för alla servrar och hanterade instanser i en prenumeration genom att växla till [Azure Security Center standard nivån](../../security-center/security-center-pricing.md).

- För en fullständig utrednings erfarenhet rekommenderar vi att du aktiverar [SQL Database granskning](../../azure-sql/database/auditing-overview.md). Med granskning kan du spåra databas händelser och skriva dem till en Gransknings logg i ett Azure Storage konto eller i Azure Log Analytics-arbetsytan.

### <a name="audit-critical-security-events"></a>Granska kritiska säkerhets händelser

Spårning av databas händelser hjälper dig att förstå databas aktiviteter. Du kan få insyn i avvikelser och avvikelser som kan tyda på affärs problem eller misstänkta säkerhets överträdelser. Den gör det också möjligt och underlättar efterlevnaden av efterlevnaden av standarder.

**Implementera**:

- Aktivera [SQL Database granskning](../../azure-sql/database/auditing-overview.md) eller [hanterad instans granskning](../managed-instance/auditing-configure.md) för att spåra databas händelser och skriva dem till en Gransknings logg i ditt Azure Storage-konto, Log Analytics arbets yta (för hands version) eller Event Hubs (för hands version).

- Gransknings loggar kan skrivas till ett Azure Storage konto, till en Log Analytics arbets yta för användning med hjälp av Azure Monitor loggar eller till händelsehubben för användning med Event Hub. Du kan konfigurera valfri kombination av dessa alternativ och gransknings loggarna skrivs till var och en.

**Bästa praxis**:

- Genom att konfigurera [SQL Database granskning](../../azure-sql/database/auditing-overview.md) på servern eller [hanterad instans granskning](../managed-instance/auditing-configure.md) till gransknings händelser, granskas alla befintliga och nyligen skapade databaser på den servern.
- Som standard innehåller gransknings principen alla åtgärder (frågor, lagrade procedurer och lyckade och misslyckade inloggningar) mot databaserna, vilket kan resultera i stora gransknings loggar. Vi rekommenderar att kunderna [konfigurerar granskning för olika typer av åtgärder och åtgärds grupper med hjälp av PowerShell](./auditing-overview.md#manage-auditing). Genom att konfigurera detta kan du kontrol lera antalet granskade åtgärder och minimera risken för händelse förlust. Med anpassade gransknings konfigurationer kan kunder bara fånga de gransknings data som behövs.
- Gransknings loggar kan förbrukas direkt i [Azure Portal](https://portal.azure.com/)eller från den lagrings plats som har kon figurer ATS.

> [!NOTE]
> Om du aktiverar granskning till Log Analytics debiteras kostnaden baserat på inmatnings taxan. Var medveten om den associerade kostnaden med hjälp av det här [alternativet](https://azure.microsoft.com/pricing/details/monitor/)eller Överväg att lagra gransknings loggarna i ett Azure Storage-konto.

**Ytterligare resurser**:

- [SQL Database granskning](../../azure-sql/database/auditing-overview.md)
- [SQL Server granskning](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>Säkra gransknings loggar

Begränsa åtkomsten till lagrings kontot för att stödja separering av uppgifter och separera DBA från granskare.

**Implementera**:

- När du sparar gransknings loggar till Azure Storage bör du se till att åtkomsten till lagrings kontot är begränsad till de lägsta säkerhets principerna. Kontrol lera vem som har åtkomst till lagrings kontot.
- Mer information finns i [auktorisera åtkomst till Azure Storage](../../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Bästa praxis**:

- Att kontrol lera åtkomsten till gransknings målet är ett viktigt begrepp i att separera DBA från granskare.

- När du granskar åtkomst till känsliga data bör du överväga att skydda data med data kryptering för att undvika att information läcker till granskaren. Mer information finns i avsnittet [skydda känsliga data som används från privilegierade användare med hög behörighet](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Säkerhets hantering

I det här avsnittet beskrivs olika aspekter och bästa praxis för att hantera dina databasers säkerhets position. Den innehåller metod tips för att se till att dina databaser är konfigurerade för att uppfylla säkerhets standarder, för att upptäcka och klassificera och spåra åtkomst till potentiellt känsliga data i dina databaser.

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Se till att databaserna har kon figurer ATS för att uppfylla rekommenderade säkerhets metoder

Förbättra databas säkerheten proaktivt genom att identifiera och åtgärda potentiella databas sårbarheter.

**Implementera**:

- Aktivera [SQL sårbarhet Assessment](/sql/relational-databases/security/sql-vulnerability-assessment) (va) för att söka igenom din databas efter säkerhets problem och för att automatiskt köra regelbundet på dina databaser.

**Bästa praxis**:

- Börja med att köra VA på dina databaser och iterera genom att åtgärda misslyckade kontroller som motsätter sig säkerhets metod tips. Konfigurera bas linjer för acceptabla konfigurationer tills genomsökningen är _felfri_ eller alla kontroller har slutförts.  

- Konfigurera regelbundna återkommande genomsökningar som ska köras en gång i veckan och konfigurera den aktuella personen att ta emot sammanfattnings meddelanden.

- Granska VA-sammanfattningen efter varje veckovis genomsökning. För påträffade sårbarheter kan du utvärdera driften från föregående genomsöknings resultat och avgöra om kontrollen ska lösas. Granska om det finns en legitim orsak till ändringen av konfigurationen.

- Lös checkar och uppdatera bas linjer där det är relevant. Skapa biljett objekt för att lösa åtgärder och spåra dessa tills de har lösts.

**Ytterligare resurser**:

- [Sårbarhetsbedömning för SQL](/sql/relational-databases/security/sql-vulnerability-assessment)
- [Tjänsten SQL sårbarhet Assessment hjälper dig att identifiera databas sårbarheter](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identifiera och tagga känsliga data

Identifiera kolumner som potentiellt innehåller känsliga data. Vad som anses vara känsligt beror på kunder, efterlevnads förordningen osv. och måste utvärderas av de användare som ansvarar för dessa data. Klassificera kolumnerna för att använda avancerade känslighets beroende-baserade gransknings-och skydds scenarier.

**Implementera**:

- Använd [identifiering och klassificering av SQL-data](data-discovery-and-classification-overview.md) för att identifiera, klassificera, märka och skydda känsliga data i dina databaser.
  - Visa klassificerings rekommendationerna som skapas av den automatiserade identifieringen på instrument panelen för SQL data identifiering och klassificering. Godkänn de relevanta klassificeringarna, så att känsliga data är beständigt märkta med klassificerings etiketter.
  - Lägg till klassificeringar manuellt för ytterligare känsliga data fält som inte har identifierats av den automatiserade mekanismen.
- Mer information finns i [identifiering och klassificering av SQL-data](/sql/relational-databases/security/sql-data-discovery-and-classification).

**Bästa praxis**:

- Övervaka klassificerings instrument panelen regelbundet för en korrekt utvärdering av databasens klassificerings tillstånd. En rapport om databas klassificerings status kan exporteras eller skrivas ut för att dela för efterlevnads-och gransknings syfte.

- Övervaka kontinuerligt status för rekommenderade känsliga data i SQL sårbarhets bedömning. Spåra den känsliga data identifierings regeln och identifiera eventuell avvikelse i de rekommenderade kolumnerna för klassificering.  

- Använd klassificering på ett sätt som är anpassat till organisationens speciella behov. Anpassa din Information Protection-princip (känslighets etiketter, informations typer, identifierings logik) i [SQL information Protection](../../security-center/security-center-info-protection-policy.md) -principen i Azure Security Center.

### <a name="track-access-to-sensitive-data"></a>Spåra åtkomst till känsliga data

Övervaka vem som har åtkomst till känsliga data och fånga frågor om känsliga data i gransknings loggar.

**Implementera**:

- Använd SQL Audit och dataklassificering i kombination.
  - I [SQL Database gransknings](../../azure-sql/database/auditing-overview.md) loggen kan du spåra åtkomst specifikt till känsliga data. Du kan också visa information, till exempel de data som har öppnats, samt dess känslighets etikett. Mer information finns i [data identifiering och klassificering](data-discovery-and-classification-overview.md) och [granskning av åtkomst till känsliga data](data-discovery-and-classification-overview.md#audit-sensitive-data).

**Bästa praxis**:

- Se metod tips för avsnitten granskning och data klassificering:
  - [Granska kritiska säkerhets händelser](#audit-critical-security-events)
  - [Identifiera och tagga känsliga data](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>Visualisera status för säkerhet och efterlevnad

Använd ett enhetligt infrastruktur säkerhets hanterings system som stärker säkerhets position för dina data Center (inklusive databaser i SQL Database). Visa en lista över rekommendationer om säkerheten för dina databaser och kompatibilitetsstatus.

**Implementera**:

- Övervaka SQL-relaterade säkerhets rekommendationer och aktiva hot i [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Vanliga säkerhetshot och eventuella begränsningar

Det här avsnittet hjälper dig att hitta säkerhets åtgärder för att skydda mot vissa angrepps vektorer. Det förväntas att de flesta begränsningar kan uppnås genom att följa en eller flera av säkerhets rikt linjerna ovan.

### <a name="security-threat-data-exfiltration"></a>Säkerhetshot: data exfiltrering

Data exfiltrering är otillåten kopiering, överföring eller hämtning av data från en dator eller server. Se en definition för [data exfiltrering](https://en.wikipedia.org/wiki/Data_exfiltration) på wikipedia.

Anslutning till server över en offentlig slut punkt visar en data exfiltrering risk eftersom kunden måste öppna sina brand väggar till offentliga IP-adresser.  

**Scenario 1**: ett program på en virtuell Azure-dator ansluter till en databas i Azure SQL Database. En falsk aktör får åtkomst till den virtuella datorn och komprometterar den. I det här scenariot innebär data exfiltrering att en extern entitet som använder den falska virtuella datorn ansluter till databasen, kopierar personliga data och lagrar den i en blob-lagring eller en annan SQL Database i en annan prenumeration.

**Scenario 2**: en rouge DBA. Det här scenariot höjs ofta av säkerhets känsliga kunder från reglerade branscher. I det här scenariot kan en användare med hög behörighet kopiera data från Azure SQL Database till en annan prenumeration som inte styrs av data ägaren.

**Potentiella begränsningar**:

I dag har Azure SQL Database och SQL-hanterad instans följande tekniker för att undvika data exfiltrering hot:

- Använd en kombination av reglerna för att tillåta och neka på NSG: er för virtuella Azure-datorer för att styra vilka regioner som kan nås från den virtuella datorn.
- Om du använder en server i SQL Database anger du följande alternativ:
  - Tillåt att Azure-tjänster stängs av.
  - Tillåt endast trafik från det undernät som innehåller din virtuella Azure-dator genom att konfigurera en brand Väggs regel för VNet.
  - Använd [privat länk](../../private-link/private-endpoint-overview.md)
- För SQL-hanterad instans använder privata IP-åtkomst som standard de första data exfiltrering för en falsk virtuell dator. Aktivera funktionen för under näts delegering på ett undernät för att automatiskt ange den mest restriktiva principen för ett undernät med SQL-hanterad instans.
- Den falska DBA-andelen är mer exponerad med SQL-hanterad instans eftersom det har ett större yta-och nätverks krav som är synliga för kunderna. Den bästa minskningen av detta är att tillämpa alla metoder i denna säkerhets guide för att förhindra det falska DBA-scenariot på första platsen (inte bara för data exfiltrering). Always Encrypted är en metod för att skydda känsliga data genom att kryptera den och hålla nyckeln otillgänglig för DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Säkerhets aspekter av affärs kontinuitet och tillgänglighet

De flesta säkerhets standarder hanterar data tillgänglighet när det gäller drifts kontinuitet, vilket uppnås genom implementering av redundans-och redundans funktioner för att undvika enskilda felpunkter. För katastrof scenarier är det en vanlig metod för att bevara säkerhets kopior av data och loggfiler.Följande avsnitt innehåller en översikt över de funktioner som är inbyggda i Azure. Den innehåller också ytterligare alternativ som kan konfigureras för att uppfylla olika behov:

- Azure erbjuder inbyggd hög tillgänglighet: [hög tillgänglighet med SQL Database-och SQL-hanterad instans](high-availability-sla.md)

- I Affärskritisk nivån finns säkerhets kopior av redundans, fullständiga och differentiella loggar och säkerhets kopior för tidpunkter som är aktiverade som standard.  
  - [Automatiserade säkerhetskopieringar](automated-backups-overview.md)
  - [Återställa en databas med hjälp av automatiska säkerhets kopieringar av databaser – återställning av plats vid tid](recovery-using-backups.md#point-in-time-restore)

- Ytterligare funktioner för affärs kontinuitet, till exempel zonens redundanta konfiguration och grupper för automatisk redundans över olika Azure-geografiska områden, kan konfigureras: 
    - [Redundant konfiguration med hög tillgänglighets zon för Premium-& Affärskritisk tjänst nivåer](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)
    - [Redundant konfiguration med hög tillgänglighets zon för Generell användning tjänst nivå](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
    - [Översikt över affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="next-steps"></a>Nästa steg

- Se [en översikt över Azure SQL Database säkerhets funktioner](security-overview.md)
