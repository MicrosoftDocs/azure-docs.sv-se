---
title: DNS-alias
description: Dina program kan ansluta till ett alias för namnet på servern för Azure SQL Database. Under tiden kan du ändra SQL Database aliaspunkter till när som helst, för att underlätta testning och så vidare.
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
ms.openlocfilehash: 6ef268b349d5a21cdbadd55ffd2199a35f650e5b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376298"
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-alias för Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database har en Domain Name System (DNS)-server. PowerShell- och REST-API:er [accepterar anrop för att skapa och hantera DNS-alias](#anchor-powershell-code-62x) för ditt [logiska SQL-servernamn.](logical-servers.md)

Ett *DNS-alias* kan användas i stället för servernamnet. Klientprogram kan använda aliaset i sina anslutningssträngar. DNS-aliaset tillhandahåller ett översättningslager som kan omdirigera dina klientprogram till olika servrar. Det här lagret hjälper dig att hitta och redigera alla klienter och deras anslutningssträngar.

Vanliga användningsområden för ett DNS-alias är följande fall:

- Skapa ett namn som är lätt att komma ihåg för en server.
- Under den inledande utvecklingen kan aliaset referera till en testserver. När programmet går live kan du ändra aliaset så att det refererar till produktionsservern. Övergången från test till produktion kräver inte några ändringar i konfigurationerna för flera klienter som ansluter till servern.
- Anta att den enda databasen i ditt program flyttas till en annan server. Du kan ändra aliaset utan att behöva ändra konfigurationerna för flera klienter.
- Under ett regionalt avbrott använder du geo-återställning för att återställa databasen på en annan server och region. Du kan ändra ditt befintliga alias så att det pekar på den nya servern så att det befintliga klientprogrammet kan ansluta till den på nytt.

## <a name="domain-name-system-dns-of-the-internet"></a>Domain Name System (DNS) för Internet

Internet är beroende av DNS. DNS översätter dina egna namn till namnet på din server.

## <a name="scenarios-with-one-dns-alias"></a>Scenarier med ett DNS-alias

Anta att du behöver växla systemet till en ny server. Tidigare behövde du hitta och uppdatera alla anslutningssträngar i varje klientprogram. Men om anslutningssträngarna nu använder ett DNS-alias måste bara en aliasegenskap uppdateras.

Funktionen DNS-alias i Azure SQL Database kan vara till hjälp i följande scenarier:

### <a name="test-to-production"></a>Testa till produktion

När du börjar utveckla klientprogrammen ska de använda ett DNS-alias i sina anslutningssträngar. Du kan göra så att egenskaperna för aliaset pekar på en testversion av servern.

Senare när det nya systemet är i produktion kan du uppdatera egenskaperna för aliaset så att det pekar på produktionsservern. Det krävs ingen ändring i klientprogrammen.

### <a name="cross-region-support"></a>Stöd för flera regioner

En haveriberedskap kan flytta servern till en annan geografisk region. För ett system som använde ett DNS-alias kan du undvika att hitta och uppdatera alla anslutningssträngar för alla klienter. I stället kan du uppdatera ett alias så att det refererar till den nya servern som nu är värd för Azure SQL Database.

## <a name="properties-of-a-dns-alias"></a>Egenskaper för ett DNS-alias

Följande egenskaper gäller för varje DNS-alias för servern:

- *Unikt namn:* Varje aliasnamn som du skapar är unikt för alla servrar, precis som servernamn.
- *Servern krävs:* Ett DNS-alias kan inte skapas om det inte refererar till exakt en server, och servern måste redan finnas. Ett uppdaterat alias måste alltid referera till exakt en befintlig server.
  - När du släpper en server tar Azure-systemet även bort alla DNS-alias som refererar till servern.
- *Inte bunden till någon region:* DNS-alias är inte bundna till en region. Alla DNS-alias kan uppdateras så att de refererar till en server som finns i alla geografiska regioner.
  - Men när du uppdaterar ett alias för att referera till en annan server måste båda servrarna finnas i samma *Azure-prenumeration.*
- *Behörigheter:* Om du vill hantera ett DNS-alias måste användaren ha *behörighet som serverdeltagare* eller högre. Mer information finns i [Komma igång med rollbaserad åtkomstkontroll i Azure i Azure Portal](../../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Hantera dina DNS-alias

Både REST API:er och PowerShell-cmdlets är tillgängliga så att du kan hantera DNS-alias programmatiskt.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API:er för att hantera dina DNS-alias

Dokumentationen för REST-API:er finns nära följande webbplats:

- [Azure SQL Database REST API](/rest/api/sql/)

DESSUTOM kan DU se REST-API:erna i GitHub på:

- [Azure SQL Database REST API:er för DNS-alias](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell för att hantera dina DNS-alias

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modulen stöds fortfarande, men all framtida utveckling är till för Az.Sql-modulen. Dessa cmdlets finns i [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska.

PowerShell-cmdlets är tillgängliga som anropar REST API:er.

Ett kodexempel på PowerShell-cmdlets som används för att hantera DNS-alias dokumenteras på:

- [PowerShell för DNS-alias att Azure SQL Database](dns-alias-powershell-create.md)

De cmdlets som används i kodexe exemplet är följande:

- [New-AzSqlServerDnsAlias:](/powershell/module/az.Sql/New-azSqlServerDnsAlias)Skapar ett nytt DNS-alias i Azure SQL Database tjänstsystemet. Aliaset refererar till server 1.
- [Get-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Get-azSqlServerDnsAlias)Hämta och lista alla DNS-alias som har tilldelats till server 1.
- [Set-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Set-azSqlServerDnsAlias)Ändrar servernamnet som aliaset är konfigurerat att referera till, från server 1 till server 2.
- [Remove-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)Ta bort DNS-aliaset från server 2 med hjälp av aliasnamnet.

## <a name="limitations"></a>Begränsningar

För närvarande har ett DNS-alias följande begränsningar:

- *Fördröjning på upp till 2 minuter:* Det tar upp till 2 minuter för ett DNS-alias att uppdateras eller tas bort.
  - Oavsett kort fördröjning stoppar aliaset omedelbart refererade klientanslutningar till den äldre servern.
- *DNS-sökning:* För tillfället är det enda auktoritativa sättet att kontrollera vilken server ett visst DNS-alias refererar till genom att utföra en [DNS-sökning.](/windows-server/administration/windows-commands/nslookup)
- _Tabellgranskning stöds inte:_ Du kan inte använda ett DNS-alias på en server som *har tabellgranskning* aktiverat för en databas.
  - Tabellgranskning är inaktuell.
  - Vi rekommenderar att du går över till [Blob Auditing](../../azure-sql/database/auditing-overview.md).

## <a name="related-resources"></a>Relaterade resurser

- [Översikt över affärskontinui med Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md), inklusive haveriberedskap.
- [Azure REST API-referens](/rest/api/azure/)
- [API för server-DNS-alias](/rest/api/sql/2020-11-01-preview/serverdnsaliases)

## <a name="next-steps"></a>Nästa steg

- [PowerShell för DNS-alias att Azure SQL Database](dns-alias-powershell-create.md)
