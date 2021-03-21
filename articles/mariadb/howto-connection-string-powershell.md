---
title: Skapa en anslutnings sträng med PowerShell – Azure Database for MariaDB
description: Den här artikeln innehåller ett Azure PowerShell exempel för att skapa en anslutnings sträng för att ansluta till Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 9dee109c701d3760c93f39e639dcfd7cae07b595
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98663731"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Så här genererar du en Azure Database for MariaDB anslutnings sträng med PowerShell

Den här artikeln visar hur du skapar en anslutnings sträng för en Azure Database for MariaDB-Server. Du kan använda en anslutnings sträng för att ansluta till en Azure Database for MariaDB från många olika program.

## <a name="requirements"></a>Krav

I den här artikeln används de resurser som har skapats i följande hand bok som utgångs punkt:

* [Snabb start: skapa en Azure Database for MariaDB-server med PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Hämta anslutningssträngen

`Get-AzMariaDbConnectionString`Cmdleten används för att skapa en anslutnings sträng för att ansluta program till Azure Database for MariaDB. I följande exempel returneras anslutnings strängen för en PHP-klient från **mydemoserver**.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Anpassa Azure Database for MariaDB Server parametrar med PowerShell](howto-configure-server-parameters-using-powershell.md)
