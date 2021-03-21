---
title: Konfigurera Server parametrar – Azure PowerShell-Azure Database for PostgreSQL
description: I den här artikeln beskrivs hur du konfigurerar tjänst parametrarna i Azure Database for PostgreSQL med PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24106e40a78d4b5607c352ffd49a310533760a66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489956"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>Anpassa Azure Database for PostgreSQL Server parametrar med PowerShell

Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for PostgreSQL-server med hjälp av PowerShell. En del av motor konfigurationerna exponeras på server nivå och kan ändras.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for postgresql-server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. PostgreSql är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> När AZ. PostgreSql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Visa lista med Server konfigurations parametrar för Azure Database for PostgreSQL Server

Om du vill visa alla ändrings bara parametrar på en server och deras värden kör du `Get-AzPostgreSqlConfiguration` cmdleten.

I följande exempel visas serverns konfigurations parametrar för servern **mydemoserver** i resurs gruppen **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

För definitionen av var och en av de angivna parametrarna, se avsnittet referens för PostgreSQL på [miljövariabler](https://www.postgresql.org/docs/12/libpq-envars.html).

## <a name="show-server-configuration-parameter-details"></a>Visa information om Server konfigurations parameter

Om du vill visa information om en viss konfigurations parameter för en server kör du `Get-AzPostgreSqlConfiguration` cmdleten och anger parametern **namn** .

Det här exemplet visar information om konfigurations parametern för **långsam \_ fråge \_ logg** Server för Server- **mydemoserver** under resurs grupp **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett parameter värde för Server konfiguration

Du kan också ändra värdet för en viss server konfigurations parameter, som uppdaterar det underliggande konfiguration svärdet för PostgreSQL-Server motorn. Använd cmdleten för att uppdatera konfigurationen `Update-AzPostgreSqlConfiguration` .

Så här uppdaterar du den **långsamma \_ fråge \_ loggs** serverns konfigurations parameter för Server **mydemoserver** under resurs grupp **myresourcegroup**.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utöka lagringen automatiskt i Azure Database for postgresql server med PowerShell](howto-auto-grow-storage-powershell.md).