---
title: DNS-alias
description: Dina program kan ansluta till ett alias för namnet på servern för Azure SQL Database. Under tiden kan du ändra SQL Database aliaset pekar på när som helst, för att under lätta testning och så vidare.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 128a57f02ebd85e4fece3c1417b8afc5c845514a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732235"
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-alias för Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database har en Domain Name System-Server (DNS). PowerShell-och REST-API: er accepterar [anrop för att skapa och hantera DNS-alias](#anchor-powershell-code-62x) för ditt [logiska SQL Server](logical-servers.md) -namn.

Ett *DNS-alias* kan användas i stället för Server namnet. Klient program kan använda aliaset i anslutnings strängarna. DNS-aliaset tillhandahåller ett översättnings lager som kan omdirigera dina klient program till olika servrar. Det här lagret är ett problem med att hitta och redigera alla klienter och deras anslutnings strängar.

Vanliga användnings områden för ett DNS-alias omfattar följande fall:

- Skapa ett lätt att komma ihåg namnet på en server.
- Under den inledande utvecklingen kan ditt alias referera till en test Server. När programmet är aktivt kan du ändra aliaset så att det refererar till produktions servern. Över gången från test till produktion kräver inte några ändringar i konfigurationerna för flera klienter som ansluter till servern.
- Anta att den enda databasen i programmet flyttas till en annan server. Du kan ändra alias utan att behöva ändra konfigurationerna för flera klienter.
- Under ett regionalt avbrott använder du geo-återställning för att återställa databasen i en annan server och region. Du kan ändra ditt befintliga alias så att det pekar på den nya servern så att det befintliga klient programmet kan ansluta till det igen.

## <a name="domain-name-system-dns-of-the-internet"></a>Internet-Domain Name System (DNS)

Internet är beroende av DNS. DNS översätter dina egna namn till namnet på din server.

## <a name="scenarios-with-one-dns-alias"></a>Scenarier med ett DNS-alias

Anta att du behöver byta system till en ny server. Tidigare var du tvungen att söka efter och uppdatera varje anslutnings sträng i varje klient program. Men nu, om anslutnings strängarna använder ett DNS-alias, måste endast egenskapen alias uppdateras.

Funktionen DNS-alias i Azure SQL Database kan hjälpa dig i följande scenarier:

### <a name="test-to-production"></a>Testa till produktion

När du börjar utveckla klient programmen använder de ett DNS-alias i sina anslutnings strängar. Du gör egenskaperna för aliaset till en test version av servern.

Senare när det nya systemet är aktivt i produktion kan du uppdatera egenskaperna för aliaset så att de pekar på produktions servern. Ingen ändring av klient programmen krävs.

### <a name="cross-region-support"></a>Stöd för flera regioner

En katastrof återställning kan flytta servern till en annan geografisk region. För ett system som har använt ett DNS-alias, kan behovet av att söka efter och uppdatera alla anslutnings strängar för alla klienter undvikas. I stället kan du uppdatera ett alias för att referera till den nya server som nu är värd för Azure SQL Database.

## <a name="properties-of-a-dns-alias"></a>Egenskaper för ett DNS-alias

Följande egenskaper gäller för varje DNS-alias för servern:

- *Unikt namn:* Varje aliasnamn som du skapar är unikt för alla servrar, precis som server namn.
- *Server krävs:* Det går inte att skapa ett DNS-alias om det inte refererar till exakt en server och servern måste redan finnas. Ett uppdaterat alias måste alltid referera till exakt en befintlig server.
  - När du släpper en server släpper Azure-systemet också alla DNS-alias som refererar till servern.
- *Inte kopplat till någon region:* DNS-alias är inte kopplade till en region. Alla DNS-alias kan uppdateras för att referera till en server som finns i en geografisk region.
  - Men när du uppdaterar ett alias för att referera till en annan server måste båda servrarna finnas i samma Azure- *prenumeration*.
- *Behörigheter:* Användaren måste ha behörighet som *Server deltagare* eller högre för att kunna hantera ett DNS-alias. Mer information finns i [Kom igång med Azure rollbaserad åtkomst kontroll i Azure Portal](../../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Hantera dina DNS-alias

Både REST-API: er och PowerShell-cmdletar finns tillgängliga så att du kan hantera dina DNS-alias program mässigt.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API: er för att hantera dina DNS-alias

Dokumentationen för REST-API: erna finns tillgänglig nära följande webbplats:

- [Azure SQL Database REST API](/rest/api/sql/)

REST-API: er kan också visas i GitHub på:

- [REST-API: er för Azure SQL Database DNS-alias](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell för att hantera dina DNS-alias

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

PowerShell-cmdletar är tillgängliga som anropar REST-API: erna.

Ett kod exempel på PowerShell-cmdletar som används för att hantera DNS-alias dokumenteras på:

- [PowerShell för DNS-alias till Azure SQL Database](dns-alias-powershell-create.md)

De cmdletar som används i kod exemplet är följande:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): skapar ett nytt DNS-alias i Azure SQL Database tjänst systemet. Aliaset refererar till Server 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Hämta och lista alla DNS-alias som har tilldelats Server 1.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): ändrar Server namnet som aliaset är konfigurerat att referera till, från Server 1 till Server 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): ta bort DNS-aliaset från Server 2 med hjälp av namnet på aliaset.

## <a name="limitations"></a>Begränsningar

För närvarande har ett DNS-alias följande begränsningar:

- *Fördröjning på upp till 2 minuter:* Det tar upp till 2 minuter för ett DNS-alias att uppdateras eller tas bort.
  - Oavsett kort fördröjning stoppar aliaset omedelbart refererade klient anslutningar till den äldre servern.
- *DNS-sökning:* För närvarande är det enda auktoritativa sättet att kontrol lera vilken server ett angivet DNS-alias refererar till genom att utföra en [DNS-sökning](/windows-server/administration/windows-commands/nslookup).
- _Tabell granskning stöds inte:_ Du kan inte använda ett DNS-alias på en server där *tabell granskning* har Aktiver ATS för en databas.
  - Tabell granskning är föråldrad.
  - Vi rekommenderar att du flyttar till [BLOB-granskning](../../azure-sql/database/auditing-overview.md).

## <a name="related-resources"></a>Relaterade resurser

- [Översikt över affärs kontinuitet med Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md), inklusive haveri beredskap.
- [Azure REST API-referens](/rest/api/azure/)
- [API för Server DNS-alias](/rest/api/sql/serverdnsaliases)

## <a name="next-steps"></a>Nästa steg

- [PowerShell för DNS-alias till Azure SQL Database](dns-alias-powershell-create.md)
