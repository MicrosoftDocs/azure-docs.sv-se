---
title: Azure Hybrid-förmån
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Använd befintliga SQL Server licenser för Azure SQL Database och SQL Managed Instance rabatter.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: b5f85e0dcb8ca70d5773b8f1c3b53e0b449ef013
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779209"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hybrid-förmån – Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

På den etablerade beräkningsnivån för köpmodellen baserad på vCore kan du byta ut dina befintliga licenser mot rabatterade priser på Azure SQL Database och Azure SQL Managed Instance med [hjälp av Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). Med den här Azure-förmånen kan du spara upp till 30 procent eller ännu högre på SQL Database & SQL Managed Instance genom att använda dina SQL Server-licenser med Software Assurance. Sidan [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) har en kalkylator som hjälper dig att fastställa besparingar.  Observera att Azure Hybrid-förmån inte gäller för Azure SQL Database serverlös.

> [!NOTE]
> Att ändra till Azure Hybrid-förmån kräver inte något driftstopp.

![Prisstruktur för virtuella kärnor](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Välj en licensmodell

Med Azure Hybrid-förmån kan du välja att endast betala för den underliggande Azure-infrastrukturen genom att använda din befintliga SQL Server-licens för själva SQL Server-databasmotorn (Base Compute-priser), eller så kan du betala för både den underliggande infrastrukturen och SQL Server-licensen (licensbaserad prissättning).

Du kan välja eller ändra din licensieringsmodell i Azure Portal: 
- När nya databaser skapas väljer du **Konfigurera databas** på fliken **Grundläggande inställningar** och väljer alternativet för att spara pengar.
- För befintliga databaser väljer **du Konfigurera** på **menyn** Inställningar och väljer alternativet för att spara pengar.

Du kan också konfigurera en ny eller befintlig databas med hjälp av något av följande API:er:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ange eller uppdatera licenstypen med hjälp av PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Så här anger eller uppdaterar du licenstypen med hjälp av Azure CLI:

- [az sql db create](/cli/azure/sql/db#az_sql_db_create)
- [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)
- [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)

# <a name="rest-api"></a>[REST-API](#tab/rest)

Ange eller uppdatera licenstypen med hjälp av följande REST API:

- [Databaser – Skapa eller uppdatera](/rest/api/sql/databases/createorupdate)
- [Databaser – Uppdatera](/rest/api/sql/databases/update)
- [Hanterade instanser – Skapa eller uppdatera](/rest/api/sql/managedinstances/createorupdate)
- [Hanterade instanser – Uppdatera](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid-förmån frågor

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Finns det dubbla användningsrättigheter med Azure Hybrid-förmån för SQL Server?

Du har 180 dagars dubbla användningsrättigheter för licensen för att säkerställa att migreringarna körs sömlöst. Efter den 180-dagarsperioden kan du bara använda SQL Server i molnet i SQL Database. Du har inte längre dubbla användningsrättigheter lokalt och i molnet.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hur skiljer sig Azure Hybrid-förmån för SQL Server från licensmobilitet?

Vi erbjuder licensmobilitetsförmåner för SQL Server kunder med Software Assurance. Detta gör det möjligt att omtilldela sina licenser till en partners delade servrar. Du kan använda den här förmånen i Azure IaaS och AWS EC2.

Azure Hybrid-förmån för SQL Server skiljer sig från licensmobilitet inom två viktiga områden:

- Det ger ekonomiska fördelar med att flytta mycket virtualiserade arbetsbelastningar till Azure. SQL Server Enterprise Edition-kunder kan få fyra kärnor i Azure i Generell användning SKU för varje kärna som de äger lokalt för mycket virtualiserade program. Licensmobilitet tillåter inte några särskilda kostnadsfördelar för att flytta virtualiserade arbetsbelastningar till molnet.
- Den tillhandahåller ett PaaS-mål på Azure (SQL Managed Instance) som är mycket kompatibelt med SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Vilka är de specifika rättigheterna för Azure Hybrid-förmån för SQL Server?

SQL Database och SQL Managed Instance kunder har följande rättigheter associerade med Azure Hybrid-förmån för SQL Server:

|Licensfotavtryck|Vad får Azure Hybrid-förmån för SQL Server dig?|
|---|---|
|SQL Server Enterprise Edition-kärnkunder med SA|<li>Kan betala baspris på hyperskala, Generell användning eller Affärskritisk SKU</li><br><li>1 kärna lokalt = 4 kärnor i hyperskala-SKU</li><br><li>Lokal 1 kärna = 4 kärnor i Generell användning SKU</li><br><li>Lokal 1 kärna = 1 kärna i Affärskritisk SKU</li>|
|SQL Server Standard Edition-kärnkunder med SA|<li>Kan betala baspris på hyperskala, Generell användning eller Affärskritisk SKU</li><br><li>Lokal 1 kärna = 1 kärna i hyperskala-SKU</li><br><li>Lokal 1 kärna = 1 kärna i Generell användning SKU</li><br><li>4 kärnor lokalt = 1 kärna i Affärskritisk SKU</li>|
|||


## <a name="next-steps"></a>Nästa steg

- Hjälp med att välja Azure SQL distributionsalternativ finns i [Välja rätt distributionsalternativ i Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- En jämförelse av SQL Database och SQL Managed Instance finns i [SQL Database & SQL Managed Instance funktioner](database/features-comparison.md).
