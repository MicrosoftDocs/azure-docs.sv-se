---
title: Serverlös SQL-pool
description: Läs om SQL-poolen utan server i Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: e1a0f4f22411e506cab3b54e955a4cdc16986fe2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667508"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>SQL-pool utan server i Azure Synapse Analytics 

Varje Azure Synapse Analytics-arbetsyta levereras med Server avsluts slut punkter för SQL-poolen som du kan använda för att fråga efter data i sjön.

SQL-poolen utan server är en fråga till tjänsten över data i data Lake. Du kan komma åt dina data med hjälp av följande funktioner:
 
- En välbekant T-SQL-syntax för att skicka frågor till data på plats utan att behöva kopiera eller läsa in data i ett specialiserat lager. 
- Integrerad anslutning via T-SQL-gränssnittet som erbjuder en mängd olika Business Intelligence-och ad hoc-frågemeddelanden, inklusive de mest populära driv rutinerna. 

SQL-poolen utan server är ett distribuerat data bearbetnings system som bygger på storskaliga data-och beräknings funktioner. Med Server lös SQL-pool kan du analysera dina Big data på några sekunder till minuter, beroende på arbets belastningen. Tack vare den inbyggda fel toleransen för körning av frågor ger systemet hög tillförlitlighet och lyckade priser även för långvariga frågor som omfattar stora data mängder.

SQL-poolen utan server är Server lös, och därför finns det ingen infrastruktur för att konfigurera eller kluster att underhålla. En standard slut punkt för den här tjänsten finns i varje Azure Synapse-arbetsyta, så att du kan börja fråga efter data så fort arbets ytan skapas. 

Det kostar inget att betala för resurser. du debiteras bara för de data som bearbetas av frågor som du kör, och därför är den här modellen en sann modell för betalning per användning.  

Om du använder Apache Spark för Azure-Synapse i din datapipeline för förberedelse av data, rengöring eller anrikning, kan du [fråga externa Spark-tabeller](develop-storage-files-spark-tables.md) som du har skapat i processen direkt från SQL-poolen utan server. Använd [privat länk](../security/how-to-connect-to-workspace-with-private-links.md) för att ta din server utan SQL-adresspool till din [hanterade arbets ytans VNet](../security/synapse-workspace-managed-vnet.md).  

## <a name="serverless-sql-pool-benefits"></a>Fördelar med server utan SQL-pool

Om du behöver utforska data i data Lake, få insikter från den eller optimera din befintliga dataomvandlings pipeline kan du dra nytta av SQL-poolen utan server. Det är lämpligt för följande scenarier:

- Grundläggande identifiering och utforskning – snabbt orsaken till data i olika format (Parquet, CSV, JSON) i data Lake, så att du kan planera hur du ska extrahera insikter från den.
- Logiskt informations lager – ge en relationell abstraktion ovanpå rå data eller olika data utan att flytta och transformera data, vilket ger alltid aktuell vy över dina data.
- Datatransformering – enkelt, skalbart och effektivt sätt att omvandla data i sjön med T-SQL, så att det kan matas till BI och andra verktyg eller läsas in i ett Relations data lager (Synapse SQL-databaser, Azure SQL Database osv.).

Olika professionella roller kan dra nytta av SQL-poolen utan server:

- Data tekniker kan utforska sjö, transformera och förbereda data med hjälp av den här tjänsten och förenkla sina pipeliner för datatransformering. Mer information hittar du i den här [självstudien](tutorial-data-analyst.md).
- Data forskare kan snabbt och enkelt tänka på innehållet och strukturen i data i sjön, tack vare funktioner som OpenRowSet och automatisk schema härledning.
- Dataanalytiker kan [utforska data och Spark-externa tabeller](develop-storage-files-spark-tables.md) som skapats av data experter eller data tekniker som använder välbekanta T-SQL-språk eller deras favorit verktyg, som kan ansluta till SQL-poolen utan server.
- BI-proffs kan snabbt [skapa Power BI rapporter ovanpå data i sjö](tutorial-connect-power-bi-desktop.md) -och Spark-tabellerna.

## <a name="how-to-start-using-serverless-sql-pool"></a>Börja använda SQL-pool utan Server

Slut punkten för SQL-poolen utan server tillhandahålls i varje Azure Synapse-arbetsyta. Du kan skapa en arbets yta och börja fråga efter data direkt med hjälp av verktyg som du är van vid.

## <a name="client-tools"></a>Klient verktyg

SQL-poolen utan Server möjliggör befintliga SQL ad-hoc-frågor och Business Intelligence verktyg för att trycka på data Lake. Eftersom det ger en välbekant T-SQL-syntax kan alla verktyg som kan upprätta SQL-erbjudanden för TDS-anslutning [ansluta till och fråga SYNAPSE SQL](connect-overview.md) på begäran. Du kan ansluta till Azure Data Studio och köra Ad hoc-frågor eller ansluta till Power BI för att få insikter på några minuter.

## <a name="t-sql-support"></a>T-SQL-stöd

SQL-pool utan server ger T-SQL frågor om Surface Area, som är något förbättrat/utökat i vissa aspekter för att få plats med frågor om att fråga om delvis strukturerade och ostrukturerade data. Dessutom stöds inte vissa aspekter av T-SQL-språket på grund av utformningen av SQL-poolen utan server, som ett exempel på att DML-funktionen inte stöds för närvarande.

- Arbets belastningen kan organiseras med hjälp av välbekanta koncept:
- Databaser-Server lös SQL-poolens slut punkt kan ha flera databaser.
- Scheman – inom en databas kan det finnas en eller flera objekt ägarskaps grupper som kallas scheman.
- Vyer
- Externa resurser – data källor, fil format och tabeller

Säkerhet kan tillämpas med:

- Inloggningar och användare
- Autentiseringsuppgifter för att kontrol lera åtkomsten till lagrings konton
- Bevilja, neka och återkalla behörigheter per objekt nivå
- Azure Active Directory-integrering

T-SQL som stöds:

- Det finns stöd för fullständig [Select](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) -arean, inklusive en majoritet av SQL-funktioner
- CETAS – SKAPA EXTERN TABELL SOM VÄLJ
- DDL-instruktioner relaterade till vyer och säkerhet

SQL-poolen utan server har ingen lokal lagring, endast metadata-objekt lagras i-databaser. Det går därför inte att använda T-SQL som är relaterade till följande begrepp:

- Tabeller
- Utlösare
- Materialiserade vyer
- Andra DDL-instruktioner än de som är relaterade till vyer och säkerhet
- DML-uttryck

### <a name="extensions"></a>Tillägg

För att möjliggöra smidig användning av frågor om data som finns i filer i data Lake, utökar SQL-poolen utan Server en befintlig [OpenRowSet](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) -funktion genom att lägga till följande funktioner:

[Fråga efter flera filer eller mappar](query-data-storage.md#query-multiple-files-or-folders)

[PARQUET-filformat](query-data-storage.md#query-parquet-files)

[Ytterligare alternativ för att arbeta med avgränsad text (fält avslutning, rad avslutning, escape-tecken)](query-data-storage.md#query-csv-files)

[Läs en vald delmängd av kolumner](query-data-storage.md#read-a-chosen-subset-of-columns)

[Schema härledning](query-data-storage.md#schema-inference)

[funktionen filename](query-data-storage.md#filename-function)

[funktionen filsökväg](query-data-storage.md#filepath-function)

[Arbeta med komplexa typer och kapslade eller upprepade data strukturer](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Säkerhet

SQL-poolen utan server erbjuder mekanismer för att skydda åtkomsten till dina data.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med Server lös SQL-pool kan du centralt hantera identiteter för databas användare och andra Microsoft-tjänster med [Azure Active Directory-integrering](../../azure-sql/database/authentication-aad-configure.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory (Azure AD) stöder [Multi-Factor Authentication](../../azure-sql/database/authentication-mfa-ssms-configure.md) (MFA) för att öka säkerheten för data och program med stöd för en enkel inloggnings process.

#### <a name="authentication"></a>Autentisering

Autentisering utan SQL-pool avser hur användare bekräftar sin identitet när de ansluter till slut punkten. Två typer av autentisering stöds:

- **SQL-autentisering**

  Den här autentiseringsmetoden använder ett användar namn och lösen ord.

- **Azure Active Directory autentisering**:

  Den här autentiseringsmetoden använder identiteter som hanteras av Azure Active Directory. För Azure AD-användare kan Multi-Factor Authentication aktive ras. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true).

#### <a name="authorization"></a>Auktorisering

Auktorisering syftar på vad en användare kan göra inom en server lös SQL-adresspool och styrs av ditt användar kontos databas roll medlemskap och behörigheter på objekt nivå.

Om SQL-autentisering används, finns SQL-användaren bara i SQL-poolen utan server och behörigheter begränsas till objekten i SQL-poolen utan server. Åtkomst till skydds bara objekt i andra tjänster (till exempel Azure Storage) kan inte beviljas till SQL-användare direkt eftersom det bara finns i omfattningen för en server lös SQL-pool. SQL-användaren måste använda en av de [typer av auktorisering som stöds](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) för att komma åt filerna.

Om Azure AD-autentisering används kan en användare logga in på en server lös SQL-pool och andra tjänster, t. ex. Azure Storage, och bevilja behörighet till Azure AD-användaren.

### <a name="access-to-storage-accounts"></a>Åtkomst till lagrings konton

En användare som är inloggad i tjänsten Server lös SQL-pool måste ha behörighet att komma åt och fråga filerna i Azure Storage. SQL-poolen utan Server stöder följande typer av auktorisering:

- **Signaturen för delad åtkomst (SAS)** ger delegerad åtkomst till resurser i lagrings kontot. Med en SAS kan du bevilja klienter åtkomst till resurser i lagrings kontot, utan att dela konto nycklar. SAS ger dig detaljerad kontroll över vilken typ av åtkomst du beviljar till klienter med SAS: giltighets intervall, beviljade behörigheter, acceptabelt IP-adressintervall, acceptabelt protokoll (https/http).

- **Användar identitet** (kallas även "vidarekoppling") är en typ av auktorisering där identiteten för den Azure AD-användare som har loggat in på en server utan SQL-pool används för att ge åtkomst till data. Azure Storage-administratören måste bevilja behörigheter till Azure AD-användare för att komma åt data innan de kan komma åt data. Den här typen av auktorisering använder Azure AD-användaren som har loggat in på en server lös SQL-pool, och därför stöds den inte för SQL-användargrupper.

## <a name="next-steps"></a>Nästa steg
Ytterligare information om slut punkts anslutning och fråga om filer finns i följande artiklar: 
- [Anslut till din slut punkt](connect-overview.md)
- [Fråga dina filer](develop-storage-files-overview.md)
