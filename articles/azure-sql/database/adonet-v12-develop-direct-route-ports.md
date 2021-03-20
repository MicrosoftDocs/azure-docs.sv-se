---
title: Portar utöver 1433
description: Klient anslutningar från ADO.NET till Azure SQL Database kan kringgå proxyn och interagera direkt med databasen med andra portar än 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, devx-track-dotnet
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 06/11/2020
ms.openlocfilehash: 0d009522ea0d0986233983f8725549b618ffb537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91444879"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portar utöver 1433 för ADO.NET 4.5
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I det här avsnittet beskrivs Azure SQL Database anslutnings beteendet för klienter som använder ADO.NET 4,5 eller en senare version.

> [!IMPORTANT]
> Information om anslutnings arkitektur finns i [Azure SQL Database anslutnings arkitektur](connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Utanför vs inuti

För att anslutningar ska kunna Azure SQL Database måste vi först fråga om klient programmet körs *utanför* eller *innanför* molnets gränser i Azure. Underavsnitten diskuterar två vanliga scenarier.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Utanför:* Klienten körs på din station ära dator

Port 1433 är den enda port som måste vara öppen på den station ära datorn som är värd för SQL Database klient programmet.

### <a name="inside-client-runs-on-azure"></a>*Inuti:* Klienten körs på Azure

När din klient körs inom Azure-molnets gränser, använder den vad vi kan anropa en *direkt väg* för att interagera med SQL Database. När en anslutning har upprättats omfattar ytterligare interaktioner mellan klienten och databasen ingen Azure SQL Database Gateway.

Ordningen är följande:

1. ADO.NET 4,5 (eller senare) initierar en kort interaktion med Azure-molnet och tar emot ett dynamiskt identifierat port nummer.

   * Det dynamiskt identifierade port numret är inom intervallet 11000-11999.
2. ADO.NET ansluter sedan till SQL Database direkt, utan mellan.
3. Frågor skickas direkt till databasen och resultaten returneras direkt till klienten.

Se till att port intervallen för 11000-11999 på Azure-klientdatorn lämnas tillgängliga för ADO.NET 4,5-klienter med SQL Database.

* I synnerhet måste portarna i intervallet vara fria från andra utgående blockerare.
* På din virtuella Azure-dator kontrollerar **Windows-brandväggen med avancerad säkerhet** port inställningarna.
  
  * Du kan använda [brand väggens användar gränssnitt](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access) för att lägga till en regel för vilken du anger **TCP** -protokollet tillsammans med ett port intervall med syntaxen som **11000-11999**.

## <a name="version-clarifications"></a>Versions klargöranden

I det här avsnittet klargöras de monikers som refererar till produkt versioner. Här visas även några par olika versioner mellan produkter.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4,0 stöder TDS 7,3-protokollet, men inte 7,4.
* ADO.NET 4,5 och senare har stöd för TDS 7,4-protokollet.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 eller senare

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4,2 eller senare (JDBC 4,0 faktiskt stöder TDS 7,4 men implementerar inte "omdirigering")

## <a name="related-links"></a>Relaterade länkar

* ADO.NET 4,6 gavs ut den 20 juli 2015. Ett blogg meddelande från .NET-teamet finns [här](https://devblogs.microsoft.com/dotnet/announcing-net-framework-4-6/).
* ADO.NET 4,5 släpptes den 15 augusti 2012. Ett blogg meddelande från .NET-teamet finns [här](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code/).
  * Ett blogg inlägg om ADO.NET 4.5.1 finns [här](https://devblogs.microsoft.com/dotnet/announcing-the-net-framework-4-5-1-preview/).

* Microsoft ODBC driver 17 för SQL Server https://aka.ms/downloadmsodbcsql

* Anslut till Azure SQL Database V12 via omdirigering https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lista över TDS-protokoll](https://www.freetds.org/)
* [Översikt över SQL Database utveckling](develop-overview.md)
* [Azure SQL Database brand vägg](firewall-configure.md)
