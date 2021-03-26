---
title: Aktivera automatisk inställning
description: Du kan aktivera automatisk justering i databasen enkelt med hjälp av Azure Portal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/03/2021
ms.openlocfilehash: 1362d8c1f15b64b9d76b28fd354cdae8919504b0
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558286"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Aktivera automatisk justering i Azure Portal för att övervaka frågor och förbättra arbets Belastningens prestanda
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database hanterar automatiskt data tjänster som kontinuerligt övervakar dina frågor och identifierar den åtgärd som du kan utföra för att förbättra arbets Belastningens prestanda. Du kan granska rekommendationer och manuellt tillämpa dem, eller låta Azure SQL Database automatiskt tillämpa korrigerande åtgärder – detta kallas **automatiskt justerings läge**.

Automatisk justering kan aktive ras på servern eller på databas nivå genom:

- [Azure Portal](automatic-tuning-enable.md#azure-portal)
- [REST API](automatic-tuning-enable.md#rest-api) -anrop
- [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) -kommandon

> [!NOTE]
> För Azure SQL-hanterad instans kan alternativet FORCE_LAST_GOOD_PLAN bara konfigureras via [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . Alternativen Azure Portal baserad konfiguration och automatiska index justeringar som beskrivs i den här artikeln gäller inte för Azure SQL-hanterad instans.

> [!NOTE]
> Att konfigurera alternativ för automatisk justering via ARM-mallen (Azure Resource Manager) stöds inte för tillfället.

## <a name="enable-automatic-tuning-on-server"></a>Aktivera automatisk justering på servern

På server nivå kan du välja att ärva konfigurationen för automatisk justering från "Azure-standardvärden" eller att inte ärva konfigurationen. Standardinställningarna för Azure är FORCE_LAST_GOOD_PLAN aktive rad, CREATE_INDEX är inaktive rad och DROP_INDEX är inaktive rad.

> [!IMPORTANT]
> Från och med mars 2020 nya Azure-standardvärden för automatisk justering enligt följande:
>
> - FORCE_LAST_GOOD_PLAN = aktive rad, CREATE_INDEX = inaktive rad och DROP_INDEX = inaktive rad.
> - Befintliga servrar utan inställningar för automatisk justering konfigureras automatiskt för att ärva Azure-standardinställningarna. Detta gäller för alla kunder som för närvarande har Server inställningar för automatisk justering i ett odefinierat tillstånd.
> - Nya servrar som skapas konfigureras automatiskt för att ärva Azure-standardvärdena (till skillnad från tidigare när konfigurationen för automatisk justering var i ett odefinierat tillstånd när en ny server skapas).

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera automatisk justering på en [Server](logical-servers.md) i Azure SQL Database, navigerar du till servern i Azure Portal och väljer sedan **Automatisk justering** i menyn.

![Skärm bild som visar automatisk justering i Azure Portal, där du kan tillämpa alternativ för en server.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Observera att alternativet **DROP_INDEX** för tillfället inte är kompatibelt med program som använder partition växlings-och index tips och bör inte aktive ras i dessa fall. Det går inte att släppa oanvända index för Premium-och Affärskritisk tjänst nivåer.

Välj de automatiska justerings alternativ som du vill aktivera och välj **Använd**.

Automatiska justerings alternativ på en server tillämpas på alla databaser på den här servern. Som standard ärver alla databaser konfigurationen från sin överordnade Server, men detta kan åsidosättas och anges för varje databas individuellt.

### <a name="rest-api"></a>REST-API

Om du vill veta mer om hur du använder en REST API för att aktivera automatisk justering på en **Server**, se [uppdatering av Server automatisk justering och hämta http-metoder](/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Aktivera automatisk justering på en enskild databas

Med Azure SQL Database kan du individuellt ange konfigurationen för automatisk justering för varje databas. På databas nivå kan du välja att ärva konfigurationen för automatisk justering från den överordnade servern, "Azure-standardvärden" eller att inte ärva konfigurationen. Standardinställningarna för Azure har angetts till FORCE_LAST_GOOD_PLAN är aktive rad, CREATE_INDEX är inaktive rad och DROP_INDEX är inaktive rad.

> [!TIP]
> Den allmänna rekommendationen är att hantera konfigurationen för automatisk justering på **Server nivå** så att samma konfigurations inställningar kan tillämpas automatiskt på alla databaser. Konfigurera automatisk justering endast på en enskild databas om du behöver att databasen har andra inställningar än andra som ärver inställningar från samma server.

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera automatisk justering på en **enskild databas** navigerar du till databasen i Azure Portal och väljer **Automatisk justering**.

Enskilda inställningar för automatisk justering kan konfigureras separat för varje databas. Du kan konfigurera ett enskilt alternativ för automatisk justering manuellt eller ange att ett alternativ ärver inställningarna från servern.

![Skärm bild som visar automatisk justering i Azure Portal, där du kan tillämpa alternativ för en enskild databas.](./media/automatic-tuning-enable/database.png)

Observera att DROP_INDEX alternativ för tillfället inte är kompatibelt med program som använder partition växlings-och index tips och inte bör aktive ras i dessa fall.

När du har valt önskad konfiguration klickar du på **Använd**.

### <a name="rest-api"></a>Rest-API

Om du vill veta mer om hur du använder en REST API för att aktivera automatisk justering i en enskild databas, se [Azure SQL Database automatisk justering uppdatering och hämta HTTP-metoder](/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Om du vill aktivera automatisk justering på en enskild databas via T-SQL ansluter du till databasen och kör följande fråga:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Om du ställer in automatisk justering på AUTO används standardinställningarna för Azure. Om du anger att den ska ÄRVAs, kommer den automatiska justerings konfigurationen att ärvas från den överordnade servern. Om du väljer Anpassad måste du konfigurera automatisk justering manuellt.

Om du vill konfigurera enskilda alternativ för automatisk justering via T-SQL ansluter du till databasen och kör frågan, till exempel den här:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Om du ställer in alternativet för enskild justering till på åsidosätts alla inställningar som databasen har ärvt och alternativet för justering aktive ras. Om du ställer in den på av åsidosätts även eventuella inställningar som databasen har ärvt och alternativet för att inaktivera justeringen. Alternativet för automatisk justering, för vilka standardvärdet har angetts, kommer att ärva konfigurationen för automatisk justering från inställningarna på server nivå.  

> [!IMPORTANT]
> När det gäller [aktiv geo-replikering](auto-failover-group-overview.md)behöver automatisk justering bara konfigureras på den primära databasen. Automatiskt tillämpade justerings åtgärder, till exempel för att skapa index som skapa eller ta bort, replikeras automatiskt till den skrivskyddade sekundära. Om du försöker aktivera automatisk justering via T-SQL på den skrivskyddade sekundära sekundären, resulterar det i ett haveri att en annan inställnings konfiguration på den skrivskyddade sekundären inte stöds.
>

För att få mer om verifieringen T-SQL-alternativ för att konfigurera automatisk justering, se [Alter Database set Options (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true).

## <a name="troubleshooting"></a>Felsökning

### <a name="automated-recommendation-management-is-disabled"></a>Automatisk rekommendations hantering har inaktiverats

I händelse av fel meddelanden om att en automatiserad rekommendations hantering har inaktiverats eller helt enkelt har inaktiverats av systemet är de vanligaste orsakerna:
- Query Store är inte aktiverat eller
- Query Store är i skrivskyddat läge för en angiven databas eller
- Frågearkivet slutade köras eftersom det använde det allokerade lagrings utrymmet.

Följande steg kan övervägas för att åtgärda det här problemet:
- Rensa Frågearkivet eller ändra data lagrings perioden till "Auto" med hjälp av T-SQL. Se [Konfigurera Rekommenderad kvarhållning och avbildnings princip för frågearkivet](./query-performance-insight-use.md#recommended-retention-and-capture-policy).
- Använd SQL Server Management Studio (SSMS) och följ dessa steg:
  - Anslut till Azure SQL Database
  - Högerklicka på databasen
  - Gå till egenskaper och klicka på Frågearkivet
  - Ändra arbets läget till Read-Write
  - Ändra lagra inspelnings läge till automatiskt
  - Ändra storleks baserat rensnings läge till automatiskt

### <a name="permissions"></a>Behörigheter

Eftersom automatisk justering är en Azure-funktion måste du använda Azures inbyggda roller för att använda den. Att endast använda SQL-autentisering är inte tillräckligt för att använda funktionen från Azure Portal.

För att kunna använda automatisk justering är den minsta behörighet som krävs för att bevilja användaren Azure: s inbyggda [SQL Database deltagar](../../role-based-access-control/built-in-roles.md#sql-db-contributor) roll. Du kan också överväga att använda högre behörighets roller som SQL Server deltagare, SQL-hanterad instans deltagare, deltagare och ägare.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurera e-postmeddelanden för automatisk justering

Om du vill ta emot automatiserade e-postaviseringar om rekommendationer som görs vid automatisk justering, se guiden [Automatisk justering e-postaviseringar](automatic-tuning-email-notifications-configure.md)

## <a name="next-steps"></a>Nästa steg

- Läs [artikeln om automatisk justering](automatic-tuning-overview.md) för att lära dig mer om automatisk justering och hur det kan hjälpa dig att förbättra prestandan.
- Se [prestanda rekommendationer](database-advisor-implement-performance-recommendations.md) för en översikt över Azure SQL Database prestanda rekommendationer.
- Mer information om hur du visar prestanda påverkan för dina vanligaste frågor finns i [fråga prestanda insikter](query-performance-insight-use.md) .