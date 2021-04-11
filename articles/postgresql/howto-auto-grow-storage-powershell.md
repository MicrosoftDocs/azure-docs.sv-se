---
title: Utöka lagringen automatiskt – Azure PowerShell – Azure Database for PostgreSQL
description: I den här artikeln beskrivs hur du kan aktivera automatisk storleks ökning med PowerShell i Azure Database for PostgreSQL.
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 05333aa4a42b821366ea7ad0a564781422fda66a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551058"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Utöka lagringen automatiskt i Azure Database for PostgreSQL server med PowerShell

I den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL Server lagring så att den växer utan att arbets belastningen påverkas.

Med automatisk storleks ökning förhindrar du att servern [når lagrings gränsen](./concepts-pricing-tiers.md#reaching-the-storage-limit) och blir skrivskyddad. För servrar med 100 GB eller mindre allokerat lagrings utrymme ökas storleken med 5 GB när det lediga utrymmet är under 10%. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas storleken med 5% när det lediga utrymmet är lägre än 10 GB. De maximala lagrings gränserna gäller enligt vad som anges i avsnittet lagring på [Azure Database for PostgreSQL pris nivåer](./concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Kom ihåg att lagringen bara kan skalas upp, inte nedåt.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for postgresql-server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. PostgreSql är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> När AZ. PostgreSql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-postgresql-server-storage-auto-grow"></a>Aktivera automatisk storleks ökning i PostgreSQL Server Storage

Aktivera automatisk storleks lagring på servern på en befintlig server med följande kommando:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Aktivera server utöka lagring automatiskt när du skapar en ny server med följande kommando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och hantera Läs repliker i Azure Database for PostgreSQL med PowerShell](howto-read-replicas-powershell.md).