---
title: Stöd för att flytta Azure SQL-resurser mellan regioner med Azure Resource-arbetskraft.
description: Granska support för att flytta Azure SQL-resurser mellan regioner med Azure Resource-arbetskraften.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452093"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Stöd för flytt av Azure SQL-resurser mellan Azure-regioner

Den här artikeln sammanfattar support och förutsättningar för att flytta Azure SQL-resurser mellan Azure-regioner med Azure Resource-arbetskraft.

## <a name="requirements"></a>Krav

Kraven sammanfattas i följande tabell.

**Funktion** | **Stöds/stöds inte** | **Information**
--- | --- | ---
**Azure SQL Database storskalig** | Stöds inte | Det går inte att flytta databaser på Azure SQL-tjänstenivån med resurs förflyttning.
**Zonredundans** | Stöds |  Flytt alternativ som stöds:<br/><br/> – Mellan regioner som har stöd för zon redundans.<br/><br/> – Mellan regioner som inte stöder zon-redundans.<br/><br/> – Mellan en region som stöder zon redundans till en region som inte stöder zon redundans.<br/><br/> – Mellan en region som inte stöder zon-redundans till en region som har stöd för zon redundans. 
**Datasynkronisering** | NAV/Sync-databas: stöds inte<br/><br/> Synkronisera medlem: stöds. | Om en synkroniserad medlem flyttas måste du konfigurera datasynkronisering till den nya mål databasen.
**Befintlig geo-replikering** | Stöds | Befintliga geo-repliker mappas om till den nya primära i mål regionen.<br/><br/> Dirigering måste initieras efter flytten. [Läs mer](../azure-sql/database/active-geo-replication-configure-portal.md)
**Transparent datakryptering (TDE) med Bring Your Own Key (BYOK)** | Stöds | [Lär dig mer](../key-vault/general/move-region.md) om att flytta nyckel valv mellan regioner.
**TDE med tjänstens hanterade nyckel** | Stöds. |  [Lär dig mer](../key-vault/general/move-region.md) om att flytta nyckel valv mellan regioner.
**Regler för dynamisk data maskering** | Stöds. | Reglerna kopieras automatiskt till mål regionen som en del av flytten. [Läs mer](../azure-sql/database/dynamic-data-masking-configure-portal.md).
**Avancerad datasäkerhet** | Stöds inte. | Lösning: Konfigurera på SQL Server nivå i mål regionen. [Läs mer](../azure-sql/database/azure-defender-for-sql.md).
**Brandväggsregler** | Stöds inte. | Lösning: Konfigurera brand Väggs regler för SQL Server i mål regionen. Brand Väggs regler på databas nivå kopieras från käll servern till mål servern. [Läs mer](../azure-sql/database/firewall-create-server-level-portal-quickstart.md).
**Gransknings principer** | Stöds inte. | Principerna återställs till standardvärdet efter flytten. [Lär dig](../azure-sql/database/auditing-overview.md) hur du återställer.
**Kvarhållningsperiod för säkerhetskopior** | Stöds. | Principer för säkerhets kopiering av käll databasen överförs till mål databasen. [Lär dig](../azure-sql/database/long-term-backup-retention-configure.md) hur du ändrar inställningarna efter flytten.
**Automatisk justering** | Stöds inte. | Lösning: Ange inställningar för automatisk justering efter flytten. [Läs mer](../azure-sql/database/automatic-tuning-enable.md).
**Databas aviseringar** | Stöds inte. | Lösning: Ange aviseringar efter flytten. [Läs mer](../azure-sql/database/alerts-insights-configure-portal.md).
**Databas för Azure SQL Server-utsträckning** | Stöds inte | Det går inte att flytta SQL Server-utsträckta databaser med resurs förflyttning.
**Azure Synapse Analytics** | Stöds inte | Det går inte att flytta Azure Synapse Analytics med resurs förflyttning.
## <a name="next-steps"></a>Nästa steg

Testa [Azure SQL-resurser](tutorial-move-region-sql.md) till en annan region med resurs förflyttning.