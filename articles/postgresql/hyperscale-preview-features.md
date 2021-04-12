---
title: Förhands gransknings funktioner i Azure Database for PostgreSQL-storskalig (citus)
description: Uppdaterad lista med funktioner som för hands version
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 5f0552e09464a6b571a665cfe3895f48c3aa8c41
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258460"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>För hands versions funktioner för PostgreSQL-skalning (citus)

Azure Database for PostgreSQL-storskalig (citus) erbjuder för hands versioner av ej frisläppta funktioner. För hands versioner tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.  Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna

## <a name="features-currently-in-preview"></a>Funktioner som för hands version

Här är de funktioner som för hands version är tillgängliga för förhands granskning:

* **[Basic-nivå](concepts-hyperscale-tiers.md)**. Kör en Server grupp enbart med en koordinator-nod och inga arbetsnoder. Ett ekonomiskt sätt att utföra den första testningen och utvecklingen och hantera små produktions arbets belastningar.
* **[Postgresql 12 och 13](concepts-hyperscale-versions.md)**.
  Använd den senaste databas versionen i Server gruppen.
* **[Citus 10](concepts-hyperscale-versions.md#citus-and-other-extension-versions)**.
  Installeras automatiskt på Server grupper som kör PostgreSQL 13.
* **[Kolumn lagring](concepts-hyperscale-columnar.md)**.
  Lagra markerade tabeller i kolumner (i stället för rader) sammanhängande på disk. Stöder komprimering på disk. Lämpligt för arbets belastningar för analys och informations lager.
* **[Läs repliker](howto-hyperscale-read-replicas-portal.md)** (endast för närvarande samma region). Alla ändringar som görs i den primära server gruppen visas i sin replik, och frågor mot repliken orsakar ingen extra belastning på originalet.
  Repliker är ett användbart verktyg för att förbättra prestandan för skrivskyddade arbets belastningar.
* **[Hanterade PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)**.
  En anslutningspool som gör att många klienter kan ansluta till Server gruppen samtidigt, samtidigt som antalet aktiva anslutningar begränsas. Den uppfyller anslutnings begär Anden och låter koordinator-noden köras smidigt.
* **[pgAudit](concepts-hyperscale-audit.md)**. Innehåller detaljerad loggning för session-och objekt granskning via standard loggnings funktionen PostgreSQL. Den ger upphov till gransknings loggar som krävs för att överföra vissa myndigheters, finansiella eller ISO-certifieringar.

### <a name="available-regions-for-preview-features"></a>Tillgängliga regioner för för hands versions funktioner

PgAudit-tillägget är tillgängligt i alla [regioner som stöds av Scale (citus)](concepts-hyperscale-configuration-options.md#regions).
De andra för hands versions funktionerna är endast tillgängliga i **USA, östra** .

## <a name="does-my-server-group-have-access-to-preview-features"></a>Har min server grupp åtkomst till för hands versions funktioner?

Om du vill ta reda på om för hands versions funktionerna i citus-servergruppen () är aktiverade går du till Server gruppens **översikts** sida i Azure Portal.
Om du ser egenskaps **nivån: Basic (för hands version)** eller **nivån: standard (för hands version)** har Server gruppen åtkomst till för hands versions funktioner.

### <a name="how-to-get-access"></a>Så här får du åtkomst

När du skapar en ny citus-Server grupp markerar du kryss rutan Aktivera för **hands versions funktioner.**

## <a name="contact-us"></a>Kontakta oss

Berätta för oss om din upplevelse med hjälp av för hands versions funktioner, via e-post [fråga Azure dB för postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).
(Den här e-postadressen är inte en teknisk support kanal. Öppna en [support förfrågan](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)för tekniska problem.)
