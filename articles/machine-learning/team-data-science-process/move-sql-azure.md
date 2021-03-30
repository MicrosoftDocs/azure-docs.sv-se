---
title: Flytta data till en Azure SQL Database-team data vetenskaps process
description: Flytta data från flata filer (CSV-eller TSV-format) eller från data som lagras i en SQL Server till en Azure SQL Database.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 42bac2df7abe00be8c0e6ffddcc9bef7ef28ba9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93309524"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Flytta data till en Azure SQL Database för Azure Machine Learning

Den här artikeln beskriver alternativen för att flytta data antingen från flata filer (CSV-eller TSV-format) eller från data som lagras i SQL Server till en Azure SQL Database. Dessa uppgifter för att flytta data till molnet ingår i team data vetenskaps processen.

Ett avsnitt som beskriver alternativ för att flytta data till SQL Server för Machine Learning finns i [Flytta data till SQL Server på en virtuell Azure-dator](move-sql-server-virtual-machine.md).

I följande tabell sammanfattas alternativen för att flytta data till en Azure SQL Database.

| <b>KÄLLICENSSERVERN</b> | <b>MÅL: Azure SQL Database</b> |
| --- | --- |
| <b>Flat fil (CSV eller TSV formaterad)</b> |[Mass infogning av SQL-fråga](#bulk-insert-sql-query) |
| <b>Lokala SQL Server</b> |1.[Exportera till flat fil](#export-flat-file)<br> 2. [guiden SQL Database migrering](#insert-tables-bcp)<br> 3. [säkerhetskopiera och Återställ databasen](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Förutsättningar
De procedurer som beskrivs här kräver att du har:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).
* Ett **Azure Storage-konto**. Du använder ett Azure Storage-konto för att lagra data i den här självstudien. Om du inte har ett Azure Storage-konto går du till artikeln [skapa ett lagrings konto](../../storage/common/storage-account-create.md) . När du har skapat lagrings kontot måste du hämta den konto nyckel som används för att få åtkomst till lagringen. Se [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
* Åtkomst till en **Azure SQL Database**. Om du måste konfigurera en Azure SQL Database ger [komma igång med Microsoft Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) information om hur du etablerar en ny instans av en Azure SQL Database.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

**Data**: migreringsprocessen visas med hjälp av [NYC taxi-datauppsättningen](https://chriswhong.com/open-data/foil_nyc_taxi/). NYC taxi-datauppsättningen innehåller information om rese data och mässor och finns i Azure Blob Storage: [NYC taxi-data](https://www.andresmh.com/nyctaxitrips/). Ett exempel på och en beskrivning av dessa filer finns i [Beskrivning av NYC taxi TRIPs-data uppsättning](sql-walkthrough.md#dataset).

Du kan antingen anpassa de procedurer som beskrivs här till en uppsättning dina egna data eller följa stegen som beskrivs i använda NYC taxi-datauppsättningen. Om du vill överföra NYC taxi-datauppsättningen till SQL Server databasen följer du proceduren som beskrivs i [Mass import av data i SQL Server Database](sql-walkthrough.md#dbload).

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> Flytta data från en platt fil källa till en Azure SQL Database
Data i flata filer (CSV eller TSV formaterade) kan flyttas till en Azure SQL Database med hjälp av en SQL-fråga för Mass inmatning.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a> Mass infogning av SQL-fråga
Stegen för proceduren med hjälp av Mass infogning av SQL-fråga liknar riktningarna för att flytta data från en platt fil källa till SQL Server på en virtuell Azure-dator. Mer information finns i [Mass infogning av SQL-fråga](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> Flytta data från SQL Server till en Azure SQL Database
Om käll informationen lagras i SQL Server finns det olika möjligheter att flytta data till en Azure SQL Database:

1. [Exportera till flat fil](#export-flat-file)
2. [Guiden SQL Database migrering](#insert-tables-bcp)
3. [Säkerhetskopiera och återställa databas](#db-migration)
4. [Azure Data Factory](#adf)

Stegen för de första tre liknar dessa avsnitt i [Flytta data till SQL Server på en virtuell Azure-dator](move-sql-server-virtual-machine.md) som behandlar samma procedurer. Länkar till relevanta avsnitt i avsnittet finns i följande instruktioner.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportera till flat fil
Stegen för att exportera till en platt fil liknar de instruktioner som beskrivs i [Exportera till Flate-fil](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Guiden SQL Database migrering
Stegen för att använda guiden SQL Database migrering liknar de riktningar som beskrivs i [guiden SQL Database migrering](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Säkerhetskopiera och återställa databas
Stegen för att använda säkerhets kopiering och återställning av databasen liknar de riktningar som anges i [säkerhets kopiering och återställning av databasen](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Lär dig hur du flyttar data till en Azure SQL Database med Azure Data Factory (ADF) i det här avsnittet, [Flytta data från en SQL Server till SQL Azure med Azure Data Factory](move-sql-azure-adf.md). Det här avsnittet visar hur du använder ADF för att flytta data från en SQL Server-databas till en Azure SQL Database via Azure Blob Storage.

Överväg att använda ADF när data måste migreras kontinuerligt med hybrid lokala och molnbaserade källor.  ADF hjälper också till när data behöver transformeras eller behöver en ny affärs logik under migreringen. Med ADF kan du schemalägga och övervaka jobb med hjälp av enkla JSON-skript som hanterar data flytten på regelbunden basis. ADF har också andra funktioner, till exempel stöd för komplexa åtgärder.