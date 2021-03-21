---
title: Använd Azure Resource Health för att övervaka databas hälsa
description: Använd Azure Resource Health för att övervaka Azure SQL Database och Azure SQL-hanterad instans hälsa, hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina SQL-resurser.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 02/26/2019
ms.openlocfilehash: 1a74a2f44ac9b03e39e9fea48a3428b82111a48c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472009"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Använd Resource Health för att felsöka anslutningar för Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Resource Health](../../service-health/resource-health-overview.md#get-started) för Azure SQL Database och Azure SQL-hanterad instans hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina SQL-resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänsten.

![Översikt](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Hälso kontroller

Resource Health fastställer hälso tillståndet för din SQL-resurs genom att undersöka lyckade och misslyckade inloggningar till resursen. För närvarande undersöker Resource Health för din SQL Database-resurs bara inloggnings fel på grund av systemfel och inte användar fel. Resource Healths status uppdateras var 1 till 2 minuter.

## <a name="health-states"></a>Hälso tillstånd

### <a name="available"></a>Tillgänglig

Statusen **tillgänglig** innebär att Resource Health inte har identifierat inloggnings fel på grund av systemfel på SQL-resursen.

![Tillgängligt](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degraderad

Statusen **Degraderad** innebär att Resource Health har identifierat mestadels lyckade inloggningar, men också några misslyckade. Detta är de flesta sannolika tillfälliga inloggnings fel. För att minska effekten av anslutnings problem som orsakas av tillfälliga inloggnings fel, implementera [logik för omförsök](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) i koden.

![Degraderad](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Inte tillgänglig

Statusen **otillgänglig** innebär att Resource Health har identifierat konsekventa inloggnings försök till din SQL-resurs. Kontakta supporten om resursen är i detta tillstånd under en längre tid.

![Inte tillgänglig](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Okänt

Hälso tillståndet för **okänd** indikerar att Resource Health inte har tagit emot information om den här resursen i mer än 10 minuter. Även om denna status inte är en slutgiltig indikation på resursens tillstånd, är det en viktig data punkt i fel söknings processen. Om resursen körs som förväntat ändras statusen för resursen till tillgänglig efter några minuter. Om du har problem med resursen kan den okända hälso statusen föreslå att en händelse i plattformen påverkar resursen.

![Okänt](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historisk information

Du kan komma åt upp till 14 dagar hälso historik i avsnittet hälso historik i Resource Health. Avsnittet innehåller även orsaken till stillestånds tiden (när det är tillgängligt) för de stillestånds tider som rapporteras av Resource Health. För närvarande visar Azure stillestånds tiden för din databas resurs med en precision på två minuter. Den faktiska avbrottstiden är förmodligen mindre än en minut. Genomsnittet är 8 sekunder.

### <a name="downtime-reasons"></a>Orsaker till stillestånds tid

När din databas upplever avbrott utförs analysen för att fastställa en orsak. När det är tillgängligt rapporteras stillestånds orsaken i avsnittet hälso historik i Resource Health. Avbrotts orsaker publiceras vanligt vis inom 45 minuter efter en händelse.

#### <a name="planned-maintenance"></a>Planerat underhåll

Azure-infrastrukturen utför regelbundet planerat underhåll – uppgraderingen av maskin-eller program varu komponenter i data centret. Medan databasen genomgår underhåll kan Azure SQL avsluta vissa befintliga anslutningar och neka nya. De inloggnings fel som uppstod under planerat underhåll är vanligt vis tillfälliga och [omprövnings logiken](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) bidrar till att minska påverkan. Kontakta supporten om du fortsätter att uppleva inloggnings fel.

#### <a name="reconfiguration"></a>Omkonfiguration

Omkonfigurationer betraktas som tillfälliga villkor och förväntas av tiden. Dessa händelser kan utlösas av belastnings utjämning eller program varu-eller maskin varu problem. Alla klient produktions program som ansluter till en moln databas bör implementera en robust logik för anslutnings [försök](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), eftersom det skulle hjälpa till att minimera dessa situationer och bör vanligt vis göra felen transparent för slutanvändaren.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [logik för återförsök för tillfälliga fel](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- [Felsöka, diagnostisera och förhindra SQL-anslutningsfel](troubleshoot-common-connectivity-issues.md).
- Läs mer om hur du [konfigurerar Resource Health-aviseringar](../../service-health/resource-health-alert-arm-template-guide.md).
- Få en översikt över [Resource Health](../../application-gateway/resource-health-overview.md).
- Läs [Resource Health vanliga frågor och svar](../../service-health/resource-health-faq.md).
