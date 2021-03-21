---
title: Innehålls referens för snabb start för enkel databas
description: Hitta en innehålls referens för alla snabb starter som hjälper dig att snabbt komma igång med enkla databaser i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184424"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Komma igång med enkla databaser i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[En enda databas](../index.yml) är fullständigt hanterad PaaS-databas (Platform as a Service) som en tjänst (DbaaS) som är en idealisk lagrings motor för de moderna molnbaserade programmen. I det här avsnittet får du lära dig hur du snabbt konfigurerar och skapar en enda databas i Azure SQL Database.

## <a name="quickstart-overview"></a>Översikt över Snabbstart

I det här avsnittet visas en översikt över tillgängliga artiklar som kan hjälpa dig att snabbt komma igång med enkla databaser. Med följande snabb Starter kan du snabbt skapa en enda databas, konfigurera en brand Väggs regel på server nivå och sedan importera en databas till den nya enkla databasen med hjälp av en `.bacpac` fil:

- [Skapa en enskild databas med hjälp av Azure Portal](single-database-create-quickstart.md).
- När databasen har skapats behöver du [skydda databasen genom att konfigurera brandväggsregler](firewall-create-server-level-portal-quickstart.md).
- Om du har en befintlig databas på SQL Server som du vill migrera till Azure SQL Database bör du installera [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) som analyserar dina databaser på SQL Server och hittar eventuella problem som kan blockera migreringen. Om du inte hittar några problem kan du exportera din databas som en `.bacpac`-fil och [importera den med hjälp av Azure-portalen eller SqlPackage](database-import.md).


## <a name="automating-management-operations"></a>Automatisera hanteringsåtgärder

Du kan använda PowerShell eller Azure CLI för att skapa, konfigurera och skala databasen.

- [Skapa och konfigurera en enskild databas med PowerShell](scripts/create-and-configure-database-powershell.md) eller [Azure CLI](scripts/create-and-configure-database-cli.md)
- [Uppdatera en enskild databas och skala resurser med PowerShell](scripts/monitor-and-scale-database-powershell.md) eller [Azure CLI](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrera till en enkel databas med minimal avbrottstid

Med de här snabbstarterna kan du snabbt skapa eller importera databasen till Azure med hjälp av en `.bacpac`-fil. Men `.bacpac` `.dacpac` filer är utformade för att snabbt flytta databaser över olika versioner av SQL Server och inom Azure SQL, eller för att implementera kontinuerlig integrering i din DevOps-pipeline. Den här metoden är dock inte avsedd för migrering av produktionsdatabaser med minimal avbrottstid, eftersom du skulle behöva sluta lägga till nya data, vänta tills exporten av källdatabasen till en `.bacpac`-fil är klar och sedan vänta tills importen till Azure SQL Database är klar. All denna väntan resulterar i avbrottstid för ditt program, särskilt för stora databaser. För att flytta produktionsdatabasen behöver du ett bättre sätt att migrera som garanterar minimal avbrottstid för migrering. För detta använder du [data migration service (DMS)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json) för att migrera databasen med minimal stillestånds tid. DMS åstadkommer detta genom att inkrementellt överföra ändringar som gjorts i källdatabasen till den enkla databas som återställs. På så sätt kan du snabbt växla programmet från käll- till måldatabas med minimal avbrottstid.

## <a name="hands-on-learning-modules"></a>Praktiska inlärnings moduler

Följande Microsoft Learn moduler hjälper dig att lära dig kostnads fritt om Azure SQL Database.

- [Etablera en databas i SQL Database att lagra program data](/learn/modules/provision-azure-sql-db/)
- [Utveckla och konfigurera ett ASP.NET-program som skickar frågor till en databas i Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)
- [Skydda databasen i Azure SQL Database](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Nästa steg

- [Här finns en lista på hög nivå över funktioner som stöds i Azure SQL Database](features-comparison.md).
- Lär dig hur du [gör databasen säkrare](secure-database-tutorial.md).
- Det finns mer avancerade anvisningar i [så använder du en enkel databas i Azure SQL Database](how-to-content-reference-guide.md).
- Hitta fler exempel skript skrivna i [PowerShell](powershell-script-content-guide.md) och [Azure CLI](az-cli-script-samples-content-guide.md).
- Läs mer om [hanterings-API: et](single-database-manage.md) som du kan använda för att konfigurera dina databaser.
- [Identifiera rätt Azure SQL Database eller Azure SQL-hanterad instans-SKU för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).