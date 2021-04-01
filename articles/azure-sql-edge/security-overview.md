---
title: Skydda Azure SQL Edge
description: Lär dig mer om säkerhet i Azure SQL Edge
keywords: SQL Edge, säkerhet
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56d5eb981aa02d9da83973d49e8df79fcd9c7e9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021022"
---
# <a name="securing-azure-sql-edge"></a>Skydda Azure SQL Edge

I och med ökningen av IoT och Edge-bearbetning mellan branscher, finns det en ökning av antalet enheter och de data som genereras från enheterna. Den ökade mängden data och antalet enhets slut punkter utgör en betydande utmaning när det gäller data säkerheten och enheterna. 

Azure SQL Edge erbjuder flera funktioner som gör det relativt enklare att skydda IoT-data i SQL Server-databaser. Azure SQL Edge skapas med samma SQL-motor som ger Microsoft SQL Server och Azure SQL samma säkerhetsfunktioner, vilket gör det enklare att utöka samma säkerhets principer och praxis från molnet till gränsen.

Precis som Microsoft SQL Server och Azure SQL, kan säkra Azure SQL Edge-distributioner visas som en serie steg som omfattar fyra områden: plattform, autentisering, objekt (inklusive data) och program som har åtkomst till systemet. 

## <a name="platform-and-system-security"></a>Plattforms-och systemsäkerhet

Plattformen för Azure SQL Edge innehåller den fysiska Docker-värden, operativ systemet på värden och nätverks systemen som ansluter den fysiska enheten till program och klienter. 

Implementering av plattforms säkerhet börjar med att behålla obehöriga användare från nätverket. Några av de bästa metoderna är, men är inte begränsat till:
- Implementera brand Väggs regler för att säkerställa organisationens säkerhets policy. 
- Se till att alla senaste säkerhets uppdateringar är installerade på den fysiska enhetens operativ system. 
- Ange och begränsa värd portar som använder för Azure SQL Edge
- Se till att rätt åtkomst kontroll tillämpas på alla data volymer som är värdar för Azure SQL Edge-data. 

Mer information om Azure SQL Edge nätverks protokoll och TDS-slutpunkter finns i [nätverks protokoll och TDS-slutpunkter](/previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105)).

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering 

### <a name="authentication"></a>Autentisering  
Autentisering är en process för att bevisa att användaren är den som han eller hon ansöker. Azure SQL Edge stöder för närvarande bara `SQL Authentication` mekanismen.

- *SQL-autentisering*:

    SQL-autentisering syftar på autentiseringen av en användare när du ansluter till Azure SQL Edge med användar namn och lösen ord. Inloggnings lösen ordet för SQL **sa** måste anges vid SQL Edge-distribution. Efter det kan ytterligare SQL-inloggningar och användare skapas av Server administratören, vilket gör det möjligt för användare att ansluta med användar namn och lösen ord.

    Mer information om hur du skapar och hanterar inloggningar och användare i SQL Edge finns i [skapa en inloggning](/sql/relational-databases/security/authentication-access/create-a-login) och [skapa databas användare](/sql/relational-databases/security/authentication-access/create-a-database-user).

### <a name="authorization"></a>Auktorisering   

Auktorisering syftar på de behörigheter som tilldelats till en användare i en databas i Azure SQL Edge och avgör vad användaren får göra. Behörigheter styrs genom att lägga till användar konton till [databas roller](/sql/relational-databases/security/authentication-access/database-level-roles) och tilldela behörigheter på databas nivå till dessa roller eller genom att bevilja användaren vissa [behörigheter på objekt nivå](/sql/relational-databases/security/permissions-database-engine). Mer information finns i [inloggningar och användare](../azure-sql/database/logins-create-manage.md).

Vi rekommenderar att du skapar anpassade roller vid behov. Lägg till användare i rollen med de minsta privilegier som krävs för att utföra jobb funktionen. Tilldela inte behörigheter direkt till användare. Server administratörs kontot är medlem i den inbyggda db_owners rollen, som har omfattande behörigheter och bör bara beviljas till några få användare med administrativa uppgifter. För program använder du [Kör som](/sql/t-sql/statements/execute-as-clause-transact-sql) för att ange körnings kontexten för den anropade modulen eller använda [program roller](/sql/relational-databases/security/authentication-access/application-roles) med begränsad behörighet. Den här metoden säkerställer att det program som ansluter till databasen har den lägsta behörighet som krävs för programmet. Genom att följa dessa rekommendationer bör du också skilja på olika uppgifter.

## <a name="database-object-security"></a>Säkerhet för databas objekt

Huvud konton är individer, grupper och processer som har beviljats åtkomst till SQL Edge. "Skydd bara objekt" är den server, databas och de objekt som databasen innehåller. Varje har en uppsättning behörigheter som kan konfigureras för att minska arean. Följande tabell innehåller information om huvud konton och skydd bara objekt.

|För information om|Se|  
|---------------------------|---------|  
|Server-och databas användare, roller och processer|[Huvud databas motor](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Säkerhet för Server-och databas objekt|[Skydd bara objekt](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Kryptering och certifikat  
 
Kryptering löser inte problem med åtkomst kontroll. Det förbättrar dock säkerheten genom att begränsa data förlust även i den sällsynta händelse att åtkomst kontroller kringgås. Om till exempel databas värddatorn är felkonfigurerad och en skadlig användare erhåller känsliga data, till exempel kreditkorts nummer, kan den stulna informationen vara oanvändbar om den är krypterad. Följande tabell innehåller mer information om kryptering i Azure SQL Edge.  
  
|För information om|Se|  
|---------------------------|---------|  
|Implementera säkra anslutningar|[Kryptera anslutningar](/sql/linux/sql-server-linux-encrypted-connections)|  
|Krypterings funktioner|[Kryptografiska funktioner &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Data kryptering i vila|[Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> De säkerhets begränsningar som beskrivs för [SQL Server på Linux](/sql/linux/sql-server-linux-security-overview) gäller även för Azure SQL Edge. 


> [!NOTE]
> Azure SQL Edge inkluderar inte verktyget MSSQL-conf. Alla konfigurationer inklusive krypterings relaterad konfiguration måste utföras via [MSSQL. conf-filen](configure.md#configure-by-using-an-mssqlconf-file) eller [miljövariablerna](configure.md#configure-by-using-environment-variables). 


I likhet med Azure SQL och Microsoft SQL Server tillhandahåller Azure SQL Edge samma mekanism för att skapa och använda certifikat för att förbättra säkerheten för objekt och anslutningar. Mer information finns i [Skapa certifikat (Transact-SQL)](/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Programsäkerhet

### <a name="client-programs"></a>Klient program

Metod tips för Azure SQL Edge Security är att skriva säkra klient program. Mer information om hur du skyddar klient program på nätverks nivå finns i [klient nätverks konfiguration](/sql/database-engine/configure-windows/client-network-configuration).

### <a name="security-catalog-views-and-functions"></a>Säkerhets katalogs visningar och funktioner  
Säkerhets information visas i flera vyer och funktioner som är optimerade för prestanda och verktyg. Följande tabell innehåller information om säkerhetsvyer och funktioner i Azure SQL Edge.  
  
|Funktioner och vyer|Länkar|  
|---------------------------|---------|  
|Vyer för säkerhets kataloger, som returnerar information om behörigheter på databas nivå och server nivå, huvud konton, roller och så vidare. Dessutom finns det katalogfiler som innehåller information om krypterings nycklar, certifikat och autentiseringsuppgifter.|[Säkerhets katalogs visningar &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Säkerhets funktioner, som returnerar information om den aktuella användaren, behörigheter och scheman.|[Säkerhets funktioner &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|Vyer för dynamisk hantering av säkerhet.|[Säkerhets-relaterade vyer och funktioner i dynamisk hantering &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Granskning 

Azure SQL Edge ger samma gransknings metoder som SQL Server. Mer information finns i [SQL Server granskning (databas motor)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine).


## <a name="next-steps"></a>Nästa steg

- [Komma igång med säkerhetsfunktioner](/sql/linux/sql-server-linux-security-get-started)
- [Köra Azure SQL Edge som en icke-rot användare](configure.md#run-azure-sql-edge-as-non-root-user)
- [Azure Security Center för IoT](../defender-for-iot/overview.md)