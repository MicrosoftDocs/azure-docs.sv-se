---
title: Hantera Läs repliker – Azure PowerShell-Azure Database for PostgreSQL
description: Lär dig hur du konfigurerar och hanterar Läs repliker i Azure Database for PostgreSQL med PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0a5547928bd7d19343c50e40ab9fcb2c335e893
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674539"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Skapa och hantera Läs repliker i Azure Database for PostgreSQL med PowerShell

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for PostgreSQL-tjänsten med hjälp av PowerShell. Mer information om Läs repliker finns i [översikten](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Du kan skapa och hantera Läs repliker med PowerShell.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for postgresql-server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. PostgreSql är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> När AZ. PostgreSql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for PostgreSQL servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att den primära servern är på någon av dessa pris nivåer.

### <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

Du kan skapa en Läs replik server med följande kommando:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzPostgreSqlServerReplica`Kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Resurs gruppen där replik servern skapas.  |
| Name | mydemoreplicaserver | Namnet på den nya replik servern som skapas. |

Om du vill skapa en skrivskyddad replik av en kors region använder du parametern **location** . I följande exempel skapas en replik i regionen **USA, västra** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSQLServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md).

Som standard skapas Läs repliker med samma server konfiguration som primär, såvida inte parametern **SKU** anges.

> [!NOTE]
> Vi rekommenderar att replik serverns konfiguration ska vara lika med eller större värden än den primära för att säkerställa att repliken kan hållas kvar med huvud servern.

### <a name="list-replicas-for-a-primary-server"></a>Lista repliker för en primär server

Om du vill visa alla repliker för en specifik primär server kör du följande kommando:

```azurepowershell-interactive
Get-AzPostgreSQLReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzPostgreSQLReplica`Kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Resurs gruppen där replik servern ska skapas.  |
| ServerName | mydemoserver | Namnet eller ID: t för den primära servern. |

### <a name="stop-a-replica-server"></a>Stoppa en replik Server

Om du stoppar en Läs replik Server befordrar Läs repliken till en oberoende Server. Du kan göra det genom att köra `Update-AzPostgreSqlServer` cmdleten och genom att ange värdet ReplicationRole till `None` .

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -ReplicationRole None
```

### <a name="delete-a-replica-server"></a>Ta bort en replik Server

Du kan ta bort en Läs replik Server genom att köra `Remove-AzPostgreSqlServer` cmdleten.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Ta bort en primär server

> [!IMPORTANT]
> Om du tar bort en primär server stoppas replikeringen till alla replik servrar och själva den primära servern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Om du vill ta bort en primär server kan du köra `Remove-AzPostgreSqlServer` cmdleten.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Starta om Azure Database for PostgreSQL server med PowerShell](howto-restart-server-powershell.md)