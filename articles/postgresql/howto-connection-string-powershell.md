---
title: Skapa en anslutnings sträng med PowerShell – Azure Database for PostgreSQL
description: Den här artikeln innehåller ett Azure PowerShell exempel för att skapa en anslutnings sträng för att ansluta till Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91708177"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Så här genererar du en Azure Database for PostgreSQL anslutnings sträng med PowerShell

Den här artikeln visar hur du skapar en anslutnings sträng för en Azure Database for PostgreSQL-Server. Du kan använda en anslutnings sträng för att ansluta till en Azure Database for PostgreSQL från många olika program.

## <a name="requirements"></a>Krav

I den här artikeln används de resurser som har skapats i följande hand bok som utgångs punkt:

* [Snabb start: skapa en Azure Database for PostgreSQL-server med PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Hämta anslutningssträngen

`Get-AzPostgreSqlConnectionString`Cmdleten används för att skapa en anslutnings sträng för att ansluta program till Azure Database for PostgreSQL. I följande exempel returneras anslutnings strängen för en PHP-klient från **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

Giltiga värden för `Client` parametern är:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anpassa Azure Database for PostgreSQL Server parametrar med PowerShell](howto-configure-server-parameters-using-powershell.md)
