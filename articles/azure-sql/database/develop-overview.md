---
title: Översikt över program utveckling
description: Läs mer om tillgängliga anslutningsbibliotek och bästa praxis för program som ansluter till SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92782985"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Översikt över program utveckling – SQL Database & SQL-hanterad instans

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Den här artikeln går igenom de grundläggande överväganden som en utvecklare bör vara medveten om när de skriver kod för att ansluta till din databas i Azure. Den här artikeln gäller för Azure SQL Database och Azure SQL-hanterad instans.

## <a name="language-and-platform"></a>Språk och plattform

Du kan använda olika [programmeringsspråk och plattformar](connect-query-content-reference-guide.md) för att ansluta och fråga Azure SQL Database. Du kan hitta [exempel program](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) som du kan använda för att ansluta till databasen.

Du kan utnyttja verktyg med öppen källkod som [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/). Azure SQL Database fungerar dessutom med Microsoft-verktyg som [Visual Studio](https://www.visualstudio.com/downloads/) och [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms). Du kan också använda API: erna Azure Portal, PowerShell och REST för att få ytterligare produktivitet.

## <a name="authentication"></a>Autentisering

Åtkomst till Azure SQL Database skyddas med inloggningar och brand väggar. Azure SQL Database stöder både SQL Server och [Azure Active Directory autentisering](authentication-aad-overview.md) av användare och inloggningar. Azure Active Directory inloggningar är bara tillgängliga i SQL-hanterade instanser. 

Lär dig mer om [att hantera databas åtkomst och inloggning](logins-create-manage.md).

## <a name="connections"></a>Anslutningar

I din klient för anslutningslogik åsidosätter du standardvärdet för timeout till att vara 30 sekunder. Standardvärdet på 15 sekunder är för kort för anslutningar som beror på internet.

Om du använder en [anslutningspool](/dotnet/framework/data/adonet/sql-server-connection-pooling), måste du stänga anslutningen så snart programmet inte aktivt använder den och inte förbereder sig för att återanvända den.

Undvik tids krävande transaktioner eftersom en infrastruktur eller ett anslutnings haveri kan återställa transaktionen. Om möjligt kan du dela upp transaktionen i flera mindre transaktioner och använda [batching för att förbättra prestanda](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Återhämtning

Azure SQL Database är en moln tjänst där du kan vänta på tillfälliga fel som inträffar i den underliggande infrastrukturen eller i kommunikationen mellan moln enheter. Även om Azure SQL Database är elastisk på de transitiva infrastruktur felen kan dessa problem påverka din anslutning. När ett tillfälligt fel uppstår när du ansluter till SQL Database, ska koden [försöka anropa igen](troubleshoot-common-connectivity-issues.md). Vi rekommenderar att logiken för omprövning använder backoff Logic, så att den inte överbelastar tjänsten med att flera klienter försöker igen samtidigt. Omprövnings logik är beroende av [fel meddelanden för SQL Database klient program](troubleshoot-common-errors-issues.md).

Mer information om hur du förbereder för planerade underhålls händelser på Azure SQL Database finns i [Planera för Azure underhålls händelser i Azure SQL Database](planned-maintenance.md).

## <a name="network-considerations"></a>Nätverksöverväganden

- På den dator som är värd för ditt klientprogram, ska du se till att brandväggen tillåter utgående TCP-kommunikation på port 1433.  Mer information: [Konfigurera en Azure SQL Database brand vägg](firewall-configure.md).
- Om klient programmet ansluter till SQL Database medan klienten körs på en virtuell dator i Azure måste du öppna vissa port intervall på den virtuella datorn. Mer information: [portar utöver 1433 för ADO.NET 4,5 och SQL Database](adonet-v12-develop-direct-route-ports.md).
- Klient anslutningar till Azure SQL Database ibland kringgå proxyn och interagera direkt med databasen. Andra portar än 1433 blir viktiga. Mer information [Azure SQL Database anslutnings arkitektur](connectivity-architecture.md) och [portar utöver 1433 för ADO.NET 4,5 och SQL Database](adonet-v12-develop-direct-route-ports.md).
- För nätverks konfiguration för en instans av SQL-hanterad instans, se [nätverks konfiguration för SQL-hanterad instans](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Nästa steg

Utforska alla funktioner i [SQL Database](sql-database-paas-overview.md) -och [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md).

Information om hur du kommer igång finns i guiderna för [Azure SQL Database](quickstart-content-reference-guide.md) och [Azure SQL Managed instances](../managed-instance/quickstart-content-reference-guide.md).