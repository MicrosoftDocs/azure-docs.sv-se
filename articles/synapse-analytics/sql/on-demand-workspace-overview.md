---
title: Serverlös SQL-pool
description: Läs mer om serverlös SQL-pool i Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f0abef835e406b442239cecd81fed5751f1c9a92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378167"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Serverlös SQL-pool i Azure Synapse Analytics 

Varje Azure Synapse Analytics-arbetsyta levereras med serverlösa SQL-poolslutpunkter som du kan använda för att fråga efter data i laken.

Serverlös SQL-pool är en frågetjänst över data i din datasjö. Det gör att du kan komma åt dina data via följande funktioner:
 
- En välbekant T-SQL-syntax för att fråga efter data på plats utan att behöva kopiera eller läsa in data i ett särskilt lager. 
- Integrerad anslutning via T-SQL-gränssnittet som erbjuder en mängd business intelligence och ad hoc-frågeverktyg, inklusive de mest populära drivrutinerna. 

Serverlös SQL-pool är ett distribuerat databearbetningssystem som skapats för storskaliga data och beräkningsfunktioner. Med en serverlös SQL-pool kan du analysera dina stordata på några sekunder, beroende på arbetsbelastningen. Tack vare den inbyggda feltoleransen för frågekörning ger systemet hög tillförlitlighet och framgång även för långvariga frågor som rör stora datamängder.

Serverlös SQL-pool är serverlös, och därför finns det ingen infrastruktur att konfigurera eller kluster att underhålla. En standardslutpunkt för den här tjänsten tillhandahålls i varje Azure Synapse arbetsyta, så du kan börja fråga efter data så fort arbetsytan har skapats. 

Det finns ingen avgift för reserverade resurser, du debiteras bara för de data som bearbetas av frågor som du kör. Därför är den här modellen en verklig betala per användning-modell.  

Om du använder Apache Spark för Azure Synapse i din datapipeline för förberedelse, rensning eller berikning av data kan du fråga externa [Spark-tabeller](develop-storage-files-spark-tables.md) som du har skapat under processen direkt från en serverlös SQL-pool. Använd [Private Link för att](../security/how-to-connect-to-workspace-with-private-links.md) föra in slutpunkten för din serverlösa SQL-pool i den [hanterade arbetsytans virtuella nätverk](../security/synapse-workspace-managed-vnet.md).  

## <a name="serverless-sql-pool-benefits"></a>Fördelar med serverlös SQL-pool

Om du behöver utforska data i datasjön, få insikter från den eller optimera din befintliga pipeline för datatransformering kan du dra nytta av att använda en serverlös SQL-pool. Det är lämpligt för följande scenarier:

- Grundläggande identifiering och utforskning – Du kan snabbt få en orsak till data i olika format (Parquet, CSV, JSON) i din datasjö, så att du kan planera hur du ska extrahera insikter från den.
- Logiskt informationslager – Tillhandahåll en relationsabstrahering ovanpå rådata eller skilda data utan att flytta och transformera data, vilket gör att du alltid kan se dina data.
- Datatransformering – Enkelt, skalbart och performant sätt att transformera data i lake med T-SQL, så att de kan matas till BI och andra verktyg eller läsas in i ett relationsdatalager (Synapse SQL-databaser, Azure SQL Database osv.).

Olika professionella roller kan dra nytta av serverlös SQL-pool:

- Datatekniker kan utforska laken, transformera och förbereda data med hjälp av den här tjänsten och förenkla sina pipelines för datatransformering. Mer information finns i den här [självstudien.](tutorial-data-analyst.md)
- Dataexperter kan snabbt få en orsak till innehållet och strukturen för data i en lake, tack vare funktioner som OPENROWSET och automatisk schemainferens.
- Dataanalytiker kan utforska data och [externa Spark-tabeller](develop-storage-files-spark-tables.md) som skapats av dataforskare eller datatekniker med hjälp av ett bekant T-SQL-språk eller deras favoritverktyg, som kan ansluta till en serverlös SQL-pool.
- BI-proffs kan [snabbt Power BI rapporter ovanpå data i lake- och](tutorial-connect-power-bi-desktop.md) Spark-tabeller.

## <a name="how-to-start-using-serverless-sql-pool"></a>Så här börjar du använda en serverlös SQL-pool

Serverlös slutpunkt för SQL-pool tillhandahålls i varje Azure Synapse arbetsyta. Du kan skapa en arbetsyta och börja köra frågor mot data direkt med hjälp av verktyg som du är bekant med.

## <a name="client-tools"></a>Klientverktyg

Serverlös SQL-pool gör det möjligt att använda befintliga AD HOC-frågor och business intelligence sql-verktyg för att utnyttja datasjön. Eftersom det tillhandahåller välbekant T-SQL-syntax kan alla verktyg som kan upprätta TDS-anslutning till SQL-erbjudanden ansluta till och [fråga Synapse SQL](connect-overview.md). Du kan ansluta med Azure Data Studio och köra ad hoc-frågor eller ansluta med Power BI för att få insikter på bara några minuter.

## <a name="t-sql-support"></a>Stöd för T-SQL

Serverlös SQL-pool erbjuder T-SQL-frågeytan, som är något förbättrad/utökad i vissa aspekter för att tillgodose upplevelser kring frågor om halvstrukturerade och ostrukturerade data. Dessutom stöds inte vissa aspekter av T-SQL-språket på grund av utformningen av en serverlös SQL-pool. DML-funktioner stöds för närvarande inte.

- Arbetsbelastningen kan organiseras med hjälp av välbekanta begrepp:
- Databaser – serverlös slutpunkt för SQL-pool kan ha flera databaser.
- Scheman – Inom en databas kan det finnas en eller flera objektägarskapsgrupper som kallas scheman.
- Vyer
- Externa resurser – datakällor, filformat och tabeller

Säkerhet kan framtvingas med hjälp av:

- Inloggningar och användare
- Autentiseringsuppgifter för att styra åtkomsten till lagringskonton
- Bevilja, neka och återkalla behörigheter per objektnivå
- Azure Active Directory-integrering

T-SQL som stöds:

- Fullständig [SELECT-yta](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) stöds, inklusive en majoritet av SQL-funktionerna
- CETAS – SKAPA EXTERN TABELL SOM VAL
- DDL-instruktioner som endast rör vyer och säkerhet

Serverlös SQL-pool har ingen lokal lagring, endast metadataobjekt lagras i databaser. Därför stöds inte T-SQL som är relaterat till följande begrepp:

- Tables
- Utlösare
- Materialiserade vyer
- DDL-instruktioner som inte är relaterade till vyer och säkerhet
- DML-uttryck

### <a name="extensions"></a>Tillägg

För att ge en smidig upplevelse för frågor om data som finns i filer i datasjön utökar serverlös SQL-pool den befintliga [OPENROWSET-funktionen](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) genom att lägga till följande funktioner:

[Fråga flera filer eller mappar](query-data-storage.md#query-multiple-files-or-folders)

[PARQUET-filformat](query-data-storage.md#query-parquet-files)

[Ytterligare alternativ för att arbeta med avgränsad text (fältavgränsare, radavgränsare, escape-tecken)](query-data-storage.md#query-csv-files)

[Läsa en vald delmängd av kolumner](query-data-storage.md#read-a-chosen-subset-of-columns)

[Schemaferens](query-data-storage.md#schema-inference)

[filename-funktion](query-data-storage.md#filename-function)

[filepath-funktion](query-data-storage.md#filepath-function)

[Arbeta med komplexa typer och kapslade eller upprepade datastrukturer](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Säkerhet

Serverlös SQL-pool erbjuder mekanismer för säker åtkomst till dina data.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med serverlös SQL-pool kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory integrering](../../azure-sql/database/authentication-aad-configure.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory (Azure AD) [](../../azure-sql/database/authentication-mfa-ssms-configure.md) stöder multifaktorautentisering (MFA) för att öka säkerheten för data och program och samtidigt stödja en process för enkel inloggning.

#### <a name="authentication"></a>Autentisering

Serverlös SQL-poolautentisering syftar på hur användare bevisar sin identitet vid anslutning till slutpunkten. Två typer av autentisering stöds:

- **SQL-autentisering**

  Den här autentiseringsmetoden använder ett användarnamn och lösenord.

- **Azure Active Directory autentisering:**

  Den här autentiseringsmetoden använder identiteter som hanteras av Azure Active Directory. För Azure AD-användare kan multifaktorautentisering aktiveras. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true).

#### <a name="authorization"></a>Auktorisering

Auktorisering avser vad en användare kan göra i en serverlös SQL-pooldatabas och styrs av ditt användarkontos databasrollmedlemskap och behörigheter på objektnivå.

Om SQL-autentisering används finns SQL-användaren bara i en serverlös SQL-pool och behörigheterna är begränsade till objekten i en serverlös SQL-pool. Åtkomst till rekursbara objekt i andra tjänster (till exempel Azure Storage) kan inte beviljas till SQL-användare direkt eftersom den bara finns i omfånget för en serverlös SQL-pool. SQL-användaren måste använda någon av de [auktoriseringstyper som stöds](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) för att komma åt filerna.

Om Azure AD-autentisering används kan en användare logga in på en serverlös SQL-pool och andra tjänster, till exempel Azure Storage, och kan bevilja behörigheter till Azure AD-användaren.

### <a name="access-to-storage-accounts"></a>Åtkomst till lagringskonton

En användare som är inloggad på den serverlösa SQL-pooltjänsten måste ha behörighet att komma åt och fråga filerna i Azure Storage. serverlös SQL-pool stöder följande auktoriseringstyper:

- **Signatur för delad åtkomst (SAS) ger** delegerad åtkomst till resurser i lagringskontot. Med en SAS kan du ge klienter åtkomst till resurser i lagringskontot utan att dela kontonycklar. En SAS ger dig detaljerad kontroll över vilken typ av åtkomst du beviljar till klienter som har SAS: giltighetsintervall, beviljade behörigheter, acceptabelt IP-adressintervall, godkänt protokoll (https/http).

- **Användaridentitet** (även kallat "direkt") är en auktoriseringstyp där identiteten för den Azure AD-användare som är inloggad i en serverlös SQL-pool används för att auktorisera åtkomst till data. Innan du kommer åt data måste Azure Storage bevilja behörigheter till Azure AD-användaren för åtkomst till data. Den här auktoriseringstypen använder den Azure AD-användare som är inloggad på en serverlös SQL-pool, och därför stöds den inte för SQL-användartyper.

## <a name="next-steps"></a>Nästa steg
Mer information om slutpunktsanslutning och frågefiler finns i följande artiklar: 
- [Ansluta till slutpunkten](connect-overview.md)
- [Köra frågor mot dina filer](develop-storage-files-overview.md)
