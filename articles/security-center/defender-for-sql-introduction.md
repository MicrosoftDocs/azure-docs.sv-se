---
title: Azure Defender för SQL – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för SQL.
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: e9a2259f1e15a94ad1f2a9eabf7d3a2f3ed419f1
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387810"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introduktion till Azure Defender för SQL

Azure Defender för SQL innehåller två Azure Defender-planer som utökar Azure Security Centers [data säkerhets paket](../azure-sql/database/azure-defender-for-sql.md) för att skydda dina databaser och deras data oavsett var de befinner sig. 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|**Azure Defender för Azure SQL Database-servrar** – allmänt tillgängliga (ga)<br>**Azure Defender för SQL-servrar på datorer** – allmänt tillgängliga (ga) |
|Priset|De två planer som utgör **Azure Defender för SQL** debiteras enligt [pris sidan](security-center-pricing.md)|
|Skyddade SQL-versioner:|[SQL på virtuella Azure-datorer](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Azure Arc-aktiverade SQL-servrar](https://docs.microsoft.com/sql/sql-server/azure-arc/overview)<br>Lokala SQL-servrar på Windows-datorer utan Azure Arc<br>Enkla Azure SQL- [databaser](../azure-sql/database/single-database-overview.md) och [elastiska pooler](../azure-sql/database/elastic-pool-overview.md)<br>[Hanterad Azure SQL-instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics (tidigare SQL DW) dedikerad SQL-pool](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Moln|![Ja ](./media/icons/yes-icon.png) kommersiella moln<br>![Ja ](./media/icons/yes-icon.png) US gov<br>![Ja ](./media/icons/yes-icon.png) Kina gov (**partiell**: delmängd av aviseringar och sårbarhets bedömning för SQL-servrar. Beteendet hot skydd är inte tillgängligt.)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Vad skyddar Azure Defender för SQL?

**Azure Defender för SQL består av** två separata Azure Defender-planer:

- **Azure Defender för Azure SQL Database-servrar** skyddar:
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Hanterad Azure SQL-instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Dedikerad SQL-pool i Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender för SQL-servrar på datorer** utökar skydds inställningarna för dina Azure-inhemska SQL-servrar för att fullständigt stödja hybrid miljöer och skydda SQL-servrar (all version som stöds) som finns i Azure, andra moln miljöer och till och med lokala datorer:
    - [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Lokala SQL-servrar:
        - [Azure Arc-aktiverad SQL Server (förhandsversion)](https://docs.microsoft.com/sql/sql-server/azure-arc/overview)
        - [SQL Server som körs på Windows-datorer utan Azure Arc](../azure-monitor/platform/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Vilka är fördelarna med Azure Defender för SQL?

Dessa två planer innehåller funktioner för att identifiera och åtgärda potentiella databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på hot mot dina databaser:

- [Sårbarhets bedömning](../azure-sql/database/sql-vulnerability-assessment.md) – genomsöknings tjänsten för att identifiera, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Utvärderings genomsökningar ger en översikt över säkerhets läget för SQL-datorer och information om eventuella säkerhets brister.

- [Avancerat skydd](../azure-sql/database/threat-detection-overview.md) – identifierings tjänsten som kontinuerligt övervakar dina SQL-servrar för hot som SQL-inmatning, brute-force-attacker och behörighets missbruk. Den här tjänsten innehåller åtgärds lösa säkerhets aviseringar i Azure Security Center med information om den misstänkta aktiviteten, vägledning om hur du minimerar hoten och alternativ för att fortsätta med din undersökning med Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Vilken typ av aviseringar finns i Azure Defender för SQL?

Säkerhets aviseringar för hot information som berikas utlöses när det finns:

- **Potentiella SQL-injektering-attacker** – inklusive sårbarheter som identifieras när program genererar en felaktig SQL-instruktion i databasen
- **Avvikande databas åtkomst och fråge mönster** – till exempel ett onormalt stort antal misslyckade inloggnings försök med andra autentiseringsuppgifter (ett brutet Force-försök)
- **Misstänkt databas aktivitet** – till exempel en legitim användare som har åtkomst till en SQL Server från en överlappande dator som kommunicerar med en krypterings-utvinning C&C-Server

Aviseringar innehåller information om den incident som utlöste dem, samt rekommendationer om hur du undersöker och åtgärdar hot.



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för SQL.

> [!div class="nextstepaction"]
> [Sök igenom SQL-servrarna efter sårbarheter med Azure Defender](defender-for-sql-usage.md)

Information om relaterade material finns i följande artiklar: 

- [Så här aktiverar du Azure Defender för SQL Database-servrar](../azure-sql/database/azure-defender-for-sql.md)
- [Listan över säkerhets aviseringar för SQL-servrar](alerts-reference.md#alerts-sql-db-and-warehouse)
