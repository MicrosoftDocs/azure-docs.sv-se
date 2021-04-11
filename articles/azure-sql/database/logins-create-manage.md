---
title: Auktorisera Server-och databas åtkomst med hjälp av inloggningar och användar konton
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Lär dig mer om hur Azure SQL Database, SQL-hanterad instans och Azure Synapse autentiserar användare för åtkomst med hjälp av inloggningar och användar konton. Lär dig också hur du beviljar databas roller och explicita behörigheter för att auktorisera inloggningar och användare för att utföra åtgärder och fråga data.
keywords: sql database-säkerhet, hantering av databassäkerhet, inloggningssäkerhet, databassäkerhet, databasåtkomst
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: a0e9cc9967509eba05bd26f3bad86eefeeb6ca3e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639475"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Auktorisera databasåtkomst till SQL Database, SQL Managed Instance och Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

I den här artikeln får du lära dig om:

- Alternativ för att konfigurera Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics så att användarna kan utföra administrativa uppgifter och komma åt data som lagras i dessa databaser.
- Åtkomst-och behörighets konfigurationen efter att du har skapat en ny server.
- Hur du lägger till inloggningar och användar konton i huvud databasen och användar kontona och sedan tilldelar de här kontona administratörs behörighet.
- Hur du lägger till användar konton i användar databaser, antingen kopplade till inloggningar eller som inneslutna användar konton.
- Konfigurera användar konton med behörigheter i användar databaser genom att använda databas roller och explicita behörigheter.

> [!IMPORTANT]
> Databaser i Azure SQL Database, Azure SQL-hanterad instans och Azure-Synapse kallas kollektivt i resten av den här artikeln som databaser, och servern refererar till den [Server](logical-servers.md) som hanterar databaser för Azure SQL Database och Azure Synapse.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

[**Autentisering**](security-overview.md#authentication) är en process för att bevisa att användaren är den som han eller hon ansöker. En användare ansluter till en databas med ett användar konto.
När en användare försöker ansluta till en databas, anger de ett användar konto och autentiseringsinformation. Användaren autentiseras med hjälp av någon av följande två autentiseringsmetoder:

- [SQL-autentisering](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Med den här autentiseringsmetoden skickar användaren ett användar konto namn och kopplat lösen ord för att upprätta en anslutning. Det här lösen ordet lagras i huvud databasen för användar konton som är länkade till en inloggning eller lagras i databasen som innehåller de användar konton som *inte är* länkade till någon inloggning.
- [Azure Active Directory-autentisering](authentication-aad-overview.md)

  Med den här autentiseringsmetoden skickar användaren ett användar konto namn och begär att tjänsten använder den autentiseringsinformation som lagrats i Azure Active Directory (Azure AD).

**Inloggningar och användare**: ett användar konto i en databas kan associeras med en inloggning som lagras i huvud databasen eller som kan vara ett användar namn som lagras i en enskild databas.

- En **inloggning** är ett enskilt konto i huvud databasen, till vilket ett användar konto i en eller flera databaser kan länkas. Med en inloggning lagras autentiseringsuppgifterna för användarkontot med inloggningen.
- Ett **användar konto** är ett enskilt konto i en databas som kan vara, men som inte behöver vara länkat till en inloggning. Med ett användarkonto som inte är länkat till en inloggning lagras autentiseringsinformation med användarkontot.

[**Behörighet att komma**](security-overview.md#authorization) åt data och utföra olika åtgärder hanteras med databas roller och explicita behörigheter. Auktorisering syftar på de behörigheter som tilldelats en användare och avgör vad användaren får göra. Auktoriseringen styrs av ditt användar kontos databas [roll medlemskap](/sql/relational-databases/security/authentication-access/database-level-roles) och [behörigheter på objekt nivå](/sql/relational-databases/security/permissions-database-engine). Ett bra tips är att du ska ge användare så få behörigheter som möjligt.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Befintliga inloggningar och användar konton när du har skapat en ny databas

När du först distribuerar Azure SQL anger du en Administratörs inloggning och ett kopplat lösen ord för inloggningen. Det här administratörs kontot kallas **Server administratör**. Följande konfiguration av inloggningar och användare i huvud databasen och användar databaser sker under distributionen:

- En SQL-inloggning med administratörs behörighet skapas med det inloggnings namn som du har angett. En [inloggning](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) är ett enskilt användar konto för inloggning till SQL Database, SQL-hanterad instans och Azure-Synapse.
- Den här inloggningen beviljas fullständig administratörs behörighet för alla databaser som en [huvud server nivå](/sql/relational-databases/security/authentication-access/principals-database-engine). Inloggningen har alla tillgängliga behörigheter och kan inte begränsas. I en SQL-hanterad instans läggs den här inloggningen till i den [fasta Server rollen sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) (den här rollen finns inte i Azure SQL Database).
- Ett [användar konto](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) som heter `dbo` skapas för den här inloggningen i varje användar databas. [Dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) -användaren har alla databas behörigheter i databasen och är mappad till den `db_owner` fasta databas rollen. Ytterligare fasta databas roller beskrivs längre fram i den här artikeln.

Om du vill identifiera administratörs konton för en databas öppnar du Azure Portal och navigerar till fliken **Egenskaper** för servern eller den hanterade instansen.

![SQL-serveradministratörer](./media/logins-create-manage/sql-admins.png)

![Skärm bild som markerar meny alternativet egenskaper.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> Inloggnings namnet för administratören kan inte ändras efter att det har skapats. Om du vill återställa lösen ordet för Server administratören går du till [Azure Portal](https://portal.azure.com), klickar på **SQL-servrar**, väljer servern i listan och klickar sedan på **Återställ lösen ord**. Om du vill återställa lösen ordet för SQL-hanterad instans går du till Azure Portal, klickar på instansen och sedan på **Återställ lösen ord**. Du kan också använda PowerShell eller Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Skapa ytterligare inloggningar och användare med administratörs behörighet

I det här läget konfigureras servern eller den hanterade instansen bara för åtkomst med hjälp av en enda SQL-inloggning och ett användar konto. Om du vill skapa ytterligare inloggningar med fullständig eller delvis administratörs behörighet har du följande alternativ (beroende på ditt distributions läge):

- **Skapa ett Azure Active Directory administratörs konto med fullständig administratörs behörighet**

  Aktivera Azure Active Directory autentisering och skapa en Azure AD-Administratörs inloggning. Ett Azure Active Directory konto kan konfigureras som administratör för Azure SQL-distributionen med fullständig administratörs behörighet. Det här kontot kan vara ett individuellt konto eller ett konto för säkerhets grupp. En Azure AD-administratör **måste** konfigureras om du vill använda Azure AD-konton för att ansluta till SQL Database, SQL-hanterad instans eller Azure-Synapse. Detaljerad information om hur du aktiverar Azure AD-autentisering för alla distributions typer för Azure SQL finns i följande artiklar:

  - [Använd Azure Active Directory autentisering för autentisering med SQL](authentication-aad-overview.md)
  - [Konfigurera och hantera Azure Active Directory-autentisering med SQL](authentication-aad-configure.md)

- **Skapa SQL-inloggningar med fullständig administratörs behörighet i SQL-hanterad instans**

  - Skapa ytterligare en SQL-inloggning i huvud databasen.
  - Lägg till inloggningen till den [fasta Server rollen sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) med hjälp av instruktionen [Alter Server Role](/sql/t-sql/statements/alter-server-role-transact-sql) . Den här inloggningen kommer att ha fullständig administratörs behörighet.
  - Du kan också skapa en [Azure AD-inloggning](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) med hjälp av syntaxen för att [Skapa inloggning](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) .

- **Skapa SQL-inloggningar med begränsade administrativa behörigheter i SQL Database**

  - Skapa ytterligare en SQL-inloggning i huvud databasen.
  - Skapa ett användar konto i huvud databasen som är kopplad till den nya inloggningen.
  - Lägg till användar kontot i `dbmanager` , `loginmanager` rollen eller både och i `master` databasen med instruktionen [Alter Role](/sql/t-sql/statements/alter-role-transact-sql) (för Azure Synapse, Använd [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) -instruktionen).

  > [!NOTE]
  > `dbmanager` och- `loginmanager` roller gäller **inte** för DISTRIBUTIONer av SQL-hanterade instanser.

  Medlemmar i dessa [särskilda huvud databas roller](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) för Azure SQL Database har behörighet att skapa och hantera databaser eller för att skapa och hantera inloggningar. I databaser som skapats av en användare som är medlem i `dbmanager` rollen mappas medlemmen till den `db_owner` fasta databas rollen och kan logga in på och hantera databasen med hjälp av `dbo` användar kontot. De här rollerna har inga uttryckliga behörigheter utanför Master-databasen.

  > [!IMPORTANT]
  > Du kan inte skapa ytterligare en SQL-inloggning med fullständig administratörs behörighet i SQL Database.

## <a name="create-accounts-for-non-administrator-users"></a>Skapa konton för användare som inte är administratörer

Du kan skapa konton för icke-administratörer på något av två sätt:

- **Skapa en inloggning**

  Skapa en SQL-inloggning i huvud databasen. Skapa sedan ett användar konto i varje databas som användaren behöver åtkomst till och koppla användar kontot till den inloggningen. Den här metoden rekommenderas när användaren måste ha åtkomst till flera databaser och du vill synkronisera lösen orden. Den här metoden har dock komplicerade metoder när de används med geo-replikering eftersom inloggningen måste skapas på både den primära servern och de sekundära servrarna. Mer information finns i [Konfigurera och hantera Azure SQL Database säkerhet för geo-återställning eller redundans](active-geo-replication-security-configure.md).
- **Skapa ett användarkonto**

  Skapa ett användar konto i databasen som en användare behöver åtkomst till (kallas även för en [innesluten användare](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Med SQL Database kan du alltid skapa den här typen av användar konto.
  - Med SQL-hanterad instans som stöder [Azure AD server-huvudobjekt](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)kan du skapa användar konton för att autentisera till den SQL-hanterade instansen utan att databas användare måste skapas som en innesluten databas användare.

  Med den här metoden lagras informationen om användarautentisering i varje databas och replikeras till geo-replikerade databaser automatiskt. Men om samma konto finns i flera databaser och du använder Azure SQL-autentisering, måste du hålla lösen orden synkroniserade manuellt. Om en användare har ett konto i olika databaser med olika lösen ord, kan det dessutom bli problem med att komma ihåg lösen orden.

> [!IMPORTANT]
> Om du vill skapa inneslutna användare som är mappade till Azure AD-identiteter måste du vara inloggad med ett Azure AD-konto som är en administratör i-databasen i Azure SQL Database. I SQL-hanterad instans kan en SQL-inloggning med `sysadmin` behörigheter också skapa en Azure AD-inloggning eller användare.

Exempel som visar hur du skapar inloggningar och användare finns i:

- [Skapa inloggning för Azure SQL Database](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)
- [Skapa inloggning för Azure SQL-hanterad instans](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true#examples-2)
- [Skapa inloggning för Azure-Synapse](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-3)
- [Skapa användare](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Skapa Azure AD-inkluderade användare](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> En säkerhets kurs som innehåller information om hur du skapar användare i Azure SQL Database finns i [Självstudier: säkra Azure SQL Database](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Använda fasta och anpassade databas roller

När du har skapat ett användar konto i en databas, antingen baserat på en inloggning eller som en innesluten användare, kan du ge användaren behörighet att utföra olika åtgärder och komma åt data i en viss databas. Du kan använda följande metoder för att auktorisera åtkomst:

- **Fasta databas roller**

  Lägg till användar kontot till en [fast databas roll](/sql/relational-databases/security/authentication-access/database-level-roles). Det finns 9 fasta databas roller, var och en med en definierad uppsättning behörigheter. De vanligaste fasta databas rollerna är: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** och **db_denydatareader**. **db_owner** används ofta för att endast ge fullständig behörighet till några användare. De andra fasta databasrollerna är användbara för att snabbt få en enkel databas i utveckling, men de rekommenderas inte för de flesta produktionsdatabaserna. **Db_datareader** den fasta databas rollen ger till exempel Läs behörighet till alla tabeller i databasen, vilket är mer än vad som är absolut nödvändigt.

  - Så här lägger du till en användare till en fast databas roll:

    - Använd instruktionen [Alter Role](/sql/t-sql/statements/alter-role-transact-sql) i Azure SQL Database. Exempel finns i [ändra roll exempel](/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure-Synapse, Använd instruktionen [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Exempel finns i [sp_addrolemember exempel](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql#examples).

- **Anpassad databas roll**

  Skapa en anpassad databas roll med instruktionen [skapa roll](/sql/t-sql/statements/create-role-transact-sql) . Med en anpassad roll kan du skapa egna användardefinierade databas roller och samtidigt ge varje roll minst de behörigheter som krävs för affärs behovet. Du kan sedan lägga till användare i den anpassade rollen. När en användare är medlem i flera roller sammanställs behörigheterna för alla.
- **Bevilja behörigheter direkt**

  Bevilja användar kontots [behörigheter](/sql/relational-databases/security/permissions-database-engine) direkt. Det finns över 100 behörigheter som individuellt kan beviljas eller nekas i SQL Database. Många av de här behörigheterna är kapslade. Till exempel inkluderar `UPDATE`-behörighet på ett schema `UPDATE`-behörighet för alla tabeller i schemat. Som i de flesta andra behörighetssystem åsidosätter ett nekande av en behörighet en beviljad. På grund av den kapslade karaktären och antalet behörigheter kan det krävas noggranna studier för att designa ett behörighetssystem som korrekt skyddar databasen. Börja med listan över behörigheter på [Behörigheter (Databasmotor)](/sql/relational-databases/security/permissions-database-engine) och granska den [stora bilden](/sql/relational-databases/security/media/database-engine-permissions.png) med behörigheter.

## <a name="using-groups"></a>Använda grupper

Effektiv åtkomst hantering använder behörigheter som tilldelats Active Directory säkerhets grupper och fasta eller anpassade roller i stället för enskilda användare.

- När du använder Azure Active Directory autentisering ska du ange Azure Active Directory användare i en Azure Active Directory säkerhets grupp. Skapa en oberoende databasanvändare för gruppen. Lägg till en eller flera databas användare som medlem i anpassade eller inbyggda databas roller med de behörigheter som är lämpliga för den gruppen av användare.

- När du använder SQL-autentisering skapar du inneslutna databas användare i databasen. Placera en eller flera databas användare i en anpassad databas roll med vissa behörigheter som är lämpliga för den gruppen av användare.

  > [!NOTE]
  > Du kan också använda grupper för icke-inneslutna databas användare.

Du bör bekanta dig med följande funktioner som kan användas för att begränsa eller utöka behörigheter:

- [Personifiering](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) och [modulsignering](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) kan användas för att säkert höja behörigheter tillfälligt.
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) kan användas som en begränsning av vilka rader en användare kan komma åt.
- [Datamaskning](dynamic-data-masking-overview.md) kan användas för att begränsa exponering av känsliga data.
- [Lagrade procedurer](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) kan användas för att begränsa de åtgärder som kan utföras i databasen.

## <a name="next-steps"></a>Nästa steg

En översikt över alla Azure SQL Database-och SQL-hanterade instans säkerhetsfunktioner finns i [säkerhets översikt](security-overview.md).
