---
title: Använda Multi-Factor Azure Active Directory-autentisering
description: Azure SQL Database, Azure SQL-hanterad instans och stöd anslutningar för Azure Synapse Analytics från SQL Server Management Studio (SSMS) med Active Directory Universal Authentication.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 9afad44bcf67478a81e75c17d0ff8ffc6d8c65aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841141"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Använda Multi-Factor Azure Active Directory-autentisering
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL-hanterad instans och stöd anslutningar för Azure Synapse Analytics från [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) med *Azure Active Directory-universell med MFA-* autentisering. Den här artikeln beskriver skillnaderna mellan olika autentiseringsalternativ och de begränsningar som är kopplade till användningen av Universal Authentication i Azure Active Directory (Azure AD) för Azure SQL.

**Hämta den senaste SSMS** – på klient datorn laddar du ned den senaste versionen av SSMS, från [nedladdnings SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


För alla funktioner som beskrivs i den här artikeln använder du minst juli 2017, version 17,2. Dialog rutan senaste anslutning bör se ut ungefär som på följande bild:

  ![Skärm bild av dialog rutan Anslut till server i SQL Server Management Studio, med inställningar för Server typ, Server namn och autentisering.](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>Autentiseringsalternativ

Det finns två icke-interaktiva autentiserings modeller för Azure AD, som kan användas i många olika program (ADO.NET, JDCB, ODC och så vidare). Dessa två metoder resulterar aldrig i popup-dialog rutor:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Den interaktiva metoden som också stöder Azure AD Multi-Factor Authentication (MFA) är: 

- `Azure Active Directory - Universal with MFA`

Azure AD MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Den ger stark autentisering med en rad enkla verifierings alternativ (telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande för mobilapp), så att användarna kan välja den metod de föredrar. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering.

En beskrivning av Azure AD Multi-Factor Authentication finns [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).
Konfigurationssteg finns i [Konfigurera Azure SQL Database-multifaktorautentisering för SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domän namn eller klient-ID-parameter

Från och med [SSMS version 17](/sql/ssms/download-sql-server-management-studio-ssms)kan användare som importeras till den aktuella Azure AD från andra Azure Active Directory som gäst användare ange Azure AD-domännamnet eller klient-ID när de ansluter. Gäst användare inkluderar användare som har bjudits in från andra Azure-annonser, Microsoft-konton som outlook.com, hotmail.com, live.com eller andra konton som gmail.com. Med den här informationen kan `Azure Active Directory - Universal with MFA` autentisering identifiera rätt autentiserings-utfärdare. Det här alternativet krävs också för att stödja Microsoft-konton (MSA) som outlook.com, hotmail.com, live.com eller icke-MSA konton. 

Alla gäst användare som vill autentiseras med Universal Authentication måste ange sitt Azure AD-domännamn eller klient-ID. Den här parametern representerar det aktuella namnet på Azure AD-domänen eller klient-ID: t som den logiska Azure SQL-servern är associerad med. Om den logiska SQL-servern till exempel är associerad med Azure AD-domänen `contosotest.onmicrosoft.com` , där användaren `joe@contosodev.onmicrosoft.com` är värd för en importerad användare från Azure AD `contosodev.onmicrosoft.com` -domänen, är domän namnet som krävs för att autentisera användaren `contosotest.onmicrosoft.com` . När användaren är en inbyggd användare av Azure AD som är kopplad till den logiska SQL-servern, och inte är ett MSA-konto, krävs inget domän namn eller klient-ID. Ange parametern (från och med SSMS version 17,2):


1. Öppna en anslutning i SSMS. Ange Server namnet och välj **Azure Active Directory-Universal med MFA** -autentisering. Lägg till det **användar namn** som du vill logga in med.
1. Välj rutan **alternativ** och gå över till fliken **anslutnings egenskaper** . I dialog rutan **Anslut till databas** fyller du i dialog rutan för din databas. Kontrol lera rutan **AD-domännamn eller klient-ID** och tillhandahålla autentiserings utfärdare, till exempel domän namnet (**CONTOSOTEST.ONMICROSOFT.com**) eller GUID för klient-ID: t. 

   ![Skärm bild av fliken anslutnings egenskaper som markerar inställningarna för Anslut till databas-och AD-domännamn eller klient-ID.](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Om du kör SSMS 18. x eller senare behövs inte längre AD-domännamnet eller klient-ID: t för gäst användare eftersom 18. x eller senare identifierar det automatiskt.

   ![Skärm bild av fliken anslutnings egenskaper i dialog rutan Anslut till server i s M S. "min databas" har marker ATS i fältet Anslut till databas.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Support för Azure AD Business till företag

> [!IMPORTANT]
> Stöd för gäst användare att ansluta till Azure SQL Database, SQL-hanterad instans och Azure Synapse utan att behöva ingå i en grupp är för närvarande en **offentlig för hands version**. Mer information finns i [Skapa Azure AD-gäst användare och ange som en Azure AD-administratör](authentication-aad-guest-users.md).

Azure AD-användare som stöds för Azure AD B2B-scenarier som gäst användare (se [Vad är Azure B2B-samarbete](../../active-directory/external-identities/what-is-b2b.md)) kan ansluta till SQL Database och Azure Synapse endast som en del av medlemmarna i en grupp som skapats i den associerade Azure AD och mappas manuellt med instruktionen [create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) i en specifik databas. Om `steve@gmail.com` till exempel är inbjuden till Azure AD `contosotest` (med Azure AD-domänen `contosotest.onmicrosoft.com` ) måste en Azure AD-grupp, till exempel, `usergroup` skapas i Azure AD som innehåller `steve@gmail.com` medlemmen. Sedan måste den här gruppen skapas för en särskild databas (till exempel `MyDatabase` ) av en Azure AD SQL-administratör eller Azure AD dbo genom att köra Transact-SQL- `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` instruktionen. 

När databas användaren har skapats `steve@gmail.com` kan användaren logga in `MyDatabase` med alternativet SSMS-autentisering `Azure Active Directory – Universal with MFA` . Som standard `usergroup` har bara Connect-behörigheten. All ytterligare data åtkomst måste [beviljas](/sql/t-sql/statements/grant-transact-sql) i databasen av en användare med tillräcklig behörighet. 

> [!NOTE]
> För SSMS 17. x, med `steve@gmail.com` som gäst användare, måste du kontrol lera rutan **AD-domännamn eller klient-ID** och lägga till AD-domännamnet `contosotest.onmicrosoft.com` i dialog rutan **anslutnings egenskap** . Alternativet **AD-domännamn eller klient-ID** stöds bara för **Azure Active Directory-Universal med MFA-** autentisering. Annars är kryss rutan nedtonad.

## <a name="universal-authentication-limitations"></a>Begränsningar för universell autentisering

- SSMS och SqlPackage.exe är de enda verktyg som för närvarande är aktiverade för MFA genom Active Directory Universal-autentisering.
- SSMS version 17,2 stöder samtidig åtkomst med flera användare med hjälp av Universal Authentication med MFA. För SSMS version 17,0 och 17,1 begränsar verktyget inloggningen för en instans av SSMS med hjälp av Universal Authentication till ett enda Azure Active Directory konto. Om du vill logga in som ett annat Azure AD-konto måste du använda en annan instans av SSMS. Den här begränsningen är begränsad till Active Directory Universal Authentication. Du kan logga in på en annan server med `Azure Active Directory - Password` autentisering, `Azure Active Directory - Integrated` autentisering eller `SQL Server Authentication` .
- SSMS stöder Active Directory Universal Authentication för Object Explorer, Frågeredigeraren och visualisering av fråge arkiv.
- SSMS version 17,2 ger stöd för DacFx-guiden för export/extrahering/distribution av data databaser. När en enskild användare har autentiserats via dialog rutan inledande autentisering med hjälp av Universal Authentication, fungerar DacFx-guiden på samma sätt som för alla andra autentiseringsmetoder.
- SSMS-Tabelldesigner stöder inte Universal-autentisering.
- Det finns inga ytterligare program varu krav för Active Directory Universal Authentication, förutom att du måste använda en version av SSMS som stöds.  
- Se följande länk för den senaste Active Directory-autentiseringsbibliotek-versionen (ADAL) för Universal Authentication: [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Nästa steg

- Konfigurationssteg finns i [Konfigurera Azure SQL Database-multifaktorautentisering för SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Ge andra åtkomst till databasen: [SQL Database autentisering och auktorisering: bevilja åtkomst](logins-create-manage.md)  
- Se till att andra kan ansluta genom brand väggen: [Konfigurera en brand Väggs regel på server nivå med hjälp av Azure Portal](firewall-configure.md)  
- [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller Azure Synapse](authentication-aad-configure.md)
- [Skapa Azure AD-gästanvändare och ange som Azure AD-administratör](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](/sql/tools/sqlpackage)  
- [Importera en BACPAC-fil till en ny databas](database-import.md)  
- [Exportera en databas till en BACPAC-fil](database-export.md)  
- C# interface [IUniversalAuthProvider-gränssnitt](/dotnet/api/microsoft.sqlserver.dac.iuniversalauthprovider)  
- När du använder **Azure Active Directory-universell med MFA-** autentisering, är ADAL-spårning tillgängligt från och med [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms). Av som standard kan du aktivera ADAL-spårning genom att använda menyn **verktyg**, **alternativ** , under Azure- **tjänster**, **Azure Cloud**, **ADAL fönstret utmatning spårnings nivå**, och sedan aktivera **utdata**  i menyn **Visa** . Spårningarna är tillgängliga i fönstret utdata när du väljer **Azure Active Directory alternativ**.