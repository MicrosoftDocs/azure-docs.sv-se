---
title: Välkommen till wingtips-appen
description: Lär dig mer om databasernas innehavande modeller, och om exempel på wingtips SaaS-program, för Azure SQL Database i moln miljön.
keywords: sql database tutorial
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: a2969ce6ceda0d1b71ec991b32f5b10acf9bfa12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780384"
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip Ticket SaaS-program
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Samma *Wingtip ticks* SaaS-program implementeras i vart och ett av tre exempel. Appen är en enkel händelse lista och biljettering SaaS app som riktar sig mot små platser – biograf, klubbar osv. Varje plats är en klient av appen och har sina egna data: information om platser, listor över händelser, kunder, biljett beställningar osv.  Appen, tillsammans med hanterings skript och självstudier, visar ett SaaS-scenario från slut punkt till slut punkt. Detta inkluderar etablering av klient organisationer, övervakning och hantering av prestanda, schema hantering och rapportering av flera klienter och analyser.

## <a name="three-saas-application-and-tenancy-patterns"></a>Tre SaaS-program och innehavande mönster

Tre versioner av appen är tillgängliga. varje utforskar ett annat databas hyres mönster på Azure SQL Database.  Först används ett fristående program per klient organisation med en egen databas. Den andra använder en app för flera klienter med en databas per klient. Det tredje exemplet använder en app med flera klient organisationer med shardade-databaser för flera innehavare.

![Tre hyres mönster][image-three-tenancy-patterns]

 Varje exempel innehåller program koden, plus hanterings skript och självstudier som utforskar en rad design-och hanterings mönster.  Varje exempel distribueras på mindre än fem minuter.  Alla tre kan distribueras sida vid sida så att du kan jämföra skillnaderna i design och hantering.

## <a name="standalone-application-per-tenant-pattern"></a>Mönster för fristående program per klient organisation

Mönstret för den fristående appen per klient organisation använder ett enda klient program med en databas för varje klient. Varje klients app, inklusive dess databas, distribueras till en separat Azure-resurs grupp. Resurs gruppen kan distribueras i tjänste leverantörens prenumeration eller klient organisationens prenumeration och hanteras av providern på klient organisationens vägnar. Det fristående programmet per klient mönster ger den bästa klient isoleringen, men är vanligt vis den dyraste eftersom det inte finns någon möjlighet att dela resurser mellan flera klienter.  Det här mönstret passar bra för program som kan vara mer komplexa och som distribueras till ett mindre antal klienter.  Med fristående distributioner kan appen anpassas för varje klient enklare än i andra mönster.  

Kolla in [självstudier][docs-tutorials-for-wingtip-sa] och kod på GitHub  [. ../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Databas per klient mönster

Databasen per klient mönster gäller för tjänste leverantörer som är intresserade av klient isolering och vill köra en centraliserad tjänst som gör det möjligt att använda delade resurser kostnads effektivt. En databas skapas för varje plats, eller klient organisation, och alla databaser hanteras centralt. Databaser kan finnas i elastiska pooler för att tillhandahålla kostnads effektiv och enkel prestanda hantering, som utnyttjar de oförutsägbara arbets belastnings mönstren för klienterna. En katalog databas innehåller mappningen mellan klienter och deras databaser. Den här mappningen hanteras med Shard Map Management-funktionerna i [Elastic Database-klient biblioteket](elastic-database-client-library.md), som ger effektiv anslutnings hantering till programmet.

Kolla in [självstudier][docs-tutorials-for-wingtip-dpt] och kod på GitHub  [. ../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Shardade databas mönster för flera innehavare

Databaser för flera klient organisationer är effektiva för tjänst leverantörer som söker efter lägre kostnad per klient och med minskad klient isolering. Det här mönstret gör det möjligt att packa upp ett stort antal klienter i en enskild databas, med kostnad per klient. Nära oändlig skalning är möjlig genom att horisontell partitionering-klienterna över flera databaser. En katalog databas mappar klienter till databaser.  

Det här mönstret tillåter också en *hybrid* modell där du kan optimera för kostnad med flera klienter i en databas, eller optimera för isolering med en enda klient i en egen databas. Valet kan göras på en klient-till-klient-basis, antingen när klienten är etablerad eller senare, utan att det påverkar programmet.  Den här modellen kan användas effektivt när grupper av klienter måste behandlas på olika sätt. Till exempel kan låg kostnads klienter tilldelas till delade databaser, medan Premium-klienter kan tilldelas till sina egna databaser. 

Kolla in [självstudier][docs-tutorials-for-wingtip-mt] och kod på GitHub  [. ../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Nästa steg

#### <a name="conceptual-descriptions"></a>Konceptuella beskrivningar

- En mer detaljerad förklaring av programmets förgrenings mönster finns på [SaaS-databasens innehavare-mönster för flera innehavare][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Självstudier och kod

- Fristående app per klient:
    - [Självstudier för fristående app][docs-tutorials-for-wingtip-sa].
    - [Kod för fristående app på GitHub][github-code-for-wingtip-sa].

- Databas per klient:
    - [Självstudier för databas per klient][docs-tutorials-for-wingtip-dpt].
    - [Kod för databas per klient, på GitHub][github-code-for-wingtip-dpt].

- Shardade flera klienter:
    - [Självstudier för shardade för flera klient organisationer][docs-tutorials-for-wingtip-mt].
    - [Code för shardade multi-Tenant på GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tre hyres mönster."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: ./saas-standaloneapp-get-started-deploy.md
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: ./saas-dbpertenant-wingtip-app-overview.md
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: ./saas-multitenantdb-get-started-deploy.md
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb