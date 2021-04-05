---
title: Synkronisera data från Azure SQL Edge med hjälp av SQL Data Sync
description: Lär dig mer om att synkronisera data från Azure SQL Edge med hjälp av Azure SQL Data Sync
keywords: SQL Edge, synkronisera data från SQL Edge, SQL Edge Data Sync
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394908"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Självstudie: synkronisera data från SQL Edge till Azure SQL Database med SQL Data Sync

I den här självstudien får du lära dig hur du använder en Azure SQL Data Sync *Sync-grupp* för att stegvis synkronisera data från Azure SQL Edge till Azure SQL Database. SQL Data Sync är en tjänst som bygger på Azure SQL Database som låter dig synkronisera data som du väljer i båda riktningarna i flera databaser i Azure SQL Database och SQL Server instanser. Mer information om SQL Data Sync finns i [Azure SQL Data Sync](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Eftersom SQL Edge bygger på de senaste versionerna av [SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation/)kan all mekanism för datasynkronisering som är tillämplig på en SQL Server-instans också användas för att synkronisera data till eller från en SQL Edge-instans som körs på en Edge-enhet.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien kräver att en Windows-dator har kon figurer ATS med [Data Sync-agenten för Azure SQL Data Sync](../azure-sql/database/sql-data-sync-agent-overview.md)

## <a name="before-you-begin"></a>Innan du börjar

* Skapa en databas i Azure SQL Database. Information om hur du skapar en databas med hjälp av Azure Portal finns [i skapa en enskild databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Skapa tabeller och andra nödvändiga objekt i din Azure SQL Database-distribution.

* Skapa nödvändiga tabeller och objekt i din Azure SQL Edge-distribution. Mer information finns i [använda SQL Database DAC-paket med SQL Edge](deploy-dacpac.md).

* Registrera Azure SQL Edge-instansen med Data Sync-agenten för Azure SQL Data Sync. Mer information finns i [lägga till en SQL Server databas](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Synkronisera data mellan en databas i Azure SQL Database och SQL Edge

Att ställa in synkronisering mellan en databas i Azure SQL Database och en SQL Edge-instans med hjälp av SQL Data Sync innefattar tre viktiga steg:  


1. Använd Azure Portal för att skapa en Sync-grupp. Mer information finns i [skapa en Sync-grupp](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). Du kan använda en enda *hubb* -databas för att skapa flera Sync-grupper för att synkronisera data från olika SQL Edge-instanser till en eller flera databaser i Azure SQL Database. 

2. Lägg till Sync-medlemmar i Sync-gruppen. Mer information finns i [lägga till Sync-medlemmar](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Konfigurera Sync-gruppen för att välja de tabeller som ska ingå i synkroniseringen. Mer information finns i [Konfigurera en Sync-grupp](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

När du har slutfört föregående steg har du en Sync-grupp som innehåller en databas i Azure SQL Database och en SQL Edge-instans.

Mer information om SQL Data Sync finns i följande artiklar:

* [Data Sync-agent för Azure SQL Data Sync](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Metod tips](../azure-sql/database/sql-data-sync-best-practices.md) och [fel sökning av problem med Azure SQL Data Sync](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Övervaka SQL Data Sync med Azure Monitor loggar](../azure-sql/database/monitor-tune-overview.md)

* [Uppdatera synkroniseringsschemat med Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) eller [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Nästa steg


* [Använd PowerShell för att synkronisera mellan Azure SQL Database och Azure SQL Edge](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). I den här självstudien ersätter du `OnPremiseServer` databas informationen med information om Azure SQL Edge.