---
title: Hantera Läs repliker – Azure PowerShell-Azure Database for MariaDB
description: Lär dig hur du konfigurerar och hanterar Läs repliker i Azure Database for MariaDB med PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 713f99f67706caded0694d88ece220373920e555
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250561"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Skapa och hantera Läs repliker i Azure Database for MariaDB med PowerShell

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for MariaDB-tjänsten med hjälp av PowerShell. Mer information om Läs repliker finns i [översikten](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Du kan skapa och hantera Läs repliker med PowerShell.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. MariaDb är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> När AZ. MariaDb PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for MariaDB servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att käll servern är i någon av dessa pris nivåer.

### <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

> [!IMPORTANT]
> När du skapar en replik för en källa som inte har några befintliga repliker startas källan om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en låg belastnings period.

Du kan skapa en Läs replik server med följande kommando:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMariaDbServerReplica`Kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Resurs gruppen där replik servern skapas.  |
| Namn | mydemoreplicaserver | Namnet på den nya replik servern som skapas. |

Om du vill skapa en skrivskyddad replik av en kors region använder du parametern **location** . I följande exempel skapas en replik i regionen **USA, västra** .

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md).

Som standard skapas Läs repliker med samma server konfiguration som källan om inte **SKU** -parametern anges.

> [!NOTE]
> Vi rekommenderar att replik serverns konfiguration måste vara lika med eller större värden än källan för att säkerställa att repliken kan hållas kvar med huvud servern.

### <a name="list-replicas-for-a-source-server"></a>Lista repliker för en käll Server

Om du vill visa alla repliker för en specifik käll server kör du följande kommando:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica`Kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Resurs gruppen där replik servern ska skapas.  |
| ServerName | mydemoserver | Namnet eller ID: t för käll servern. |

### <a name="delete-a-replica-server"></a>Ta bort en replik Server

Du kan ta bort en Läs replik Server genom att köra `Remove-AzMariaDbServer` cmdleten.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Ta bort en käll Server

> [!IMPORTANT]
> Om du tar bort en källserver stoppas replikeringen till alla replikservrar och själva källservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Om du vill ta bort en käll Server kan du köra `Remove-AzMariaDbServer` cmdleten.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Starta om Azure Database for MariaDB server med PowerShell](howto-restart-server-powershell.md)