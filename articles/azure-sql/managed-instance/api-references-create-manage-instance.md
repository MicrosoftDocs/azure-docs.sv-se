---
title: API för hantering referens för Azure SQL Managed Instance
description: Lär dig mer om att skapa och konfigurera hanterade instanser av Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 148b24aea42072f1901c76c7a09a126340ef9951
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784379"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Hanterad API-referens för Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Du kan skapa och konfigurera hanterade instanser av Azure SQL Managed Instance med hjälp av Azure Portal, PowerShell, Azure CLI, REST API och Transact-SQL. I den här artikeln hittar du en översikt över de funktioner och det API som du kan använda för att skapa och konfigurera hanterade instanser.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Skapa en hanterad instans

En snabbstart som visar hur du skapar en hanterad instans finns i [Snabbstart: Skapa en hanterad instans](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: Skapa och konfigurera hanterade instanser

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets, se [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRM-modulerna är betydligt identiska.

Om du vill skapa och hantera hanterade instanser Azure PowerShell med hjälp av följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell kan du gå [till Installera Azure PowerShell modulen](/powershell/azure/install-az-ps).

> [!TIP]
> Exempelskript för PowerShell finns i [Snabbstartsskript: Skapa en hanterad instans med hjälp av ett PowerShell-bibliotek.](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell)

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Skapar en hanterad instans. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Returnerar information om en hanterad instans.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Anger egenskaper för en hanterad instans.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Tar bort en hanterad instans.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Hämtar en lista över hanteringsåtgärder som utförts på den hanterade instansen eller en specifik åtgärd.|
|[Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Avbryter den specifika hanteringsåtgärd som utförs på den hanterade instansen.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Skapar en SQL Managed Instance databas.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|Returnerar information om en SQL Managed Instance databas.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Tar bort en SQL Managed Instance databas.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Återställer en SQL Managed Instance databas.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: Skapa och konfigurera hanterade instanser

Om du vill skapa och konfigurera hanterade instanser [med Azure CLI](/cli/azure)använder du följande Azure [CLI-kommandon för att SQL Managed Instance](/cli/azure/sql/mi). Använd [Azure Cloud Shell](../../cloud-shell/overview.md) för att köra CLI i webbläsaren eller [installera det](/cli/azure/install-azure-cli) på macOS, Linux eller Windows.

> [!TIP]
> En Snabbstart för Azure CLI finns i [Arbeta med SQL Managed Instance med Azure CLI.](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql mi create](/cli/azure/sql/mi#az_sql_mi_create) |Skapar en hanterad instans.|
|[az sql mi list](/cli/azure/sql/mi#az_sql_mi_list)|Visar en lista över tillgängliga hanterade instanser.|
|[az sql mi show](/cli/azure/sql/mi#az_sql_mi_show)|Hämtar information om en hanterad instans.|
|[az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)|Uppdaterar en hanterad instans.|
|[az sql mi delete](/cli/azure/sql/mi#az_sql_mi_delete)|Tar bort en hanterad instans.|
|[az sql mi op list](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Hämtar en lista över hanteringsåtgärder som utförs på den hanterade instansen.|
|[az sql mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Hämtar den specifika hanteringsåtgärd som utförs på den hanterade instansen.|
|[az sql mi op cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Avbryter den specifika hanteringsåtgärd som utförs på den hanterade instansen.|
|[az sql midb create](/cli/azure/sql/midb#az_sql_midb_create) |Skapar en hanterad databas.|
|[az sql midb list](/cli/azure/sql/midb#az_sql_midb_list)|Visar en lista över tillgängliga hanterade databaser.|
|[az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore)|Återställer en hanterad databas.|
|[az sql midb delete](/cli/azure/sql/midb#az_sql_midb_delete)|Tar bort en hanterad databas.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Skapa och konfigurera instansdatabaser

Om du vill skapa och konfigurera instansdatabaser när den hanterade instansen har skapats använder du följande T-SQL-kommandon. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)eller något annat program som kan ansluta till en server och skicka Transact-SQL-kommandon.

> [!TIP]
> Snabbstarter som visar hur du konfigurerar och ansluter till en hanterad instans med hjälp av SQL Server Management Studio i Microsoft Windows finns i [Snabbstart:](connect-vm-instance-configure.md) Konfigurera en virtuell Azure-dator för att ansluta till Azure SQL Managed Instance och Snabbstart: Konfigurera en [punkt-till-plats-anslutning till Azure SQL Managed Instance från en lokal](point-to-site-p2s-configure.md)plats.

> [!IMPORTANT]
> Du kan inte skapa eller ta bort en hanterad instans med Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Skapar en ny instansdatabas i SQL Managed Instance. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Ändrar en instansdatabas i SQL Managed Instance.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: Skapa och konfigurera hanterade instanser

Om du vill skapa och konfigurera hanterade instanser använder du dessa REST API begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Hanterade instanser – Skapa eller uppdatera](/rest/api/sql/managedinstances/createorupdate)|Skapar eller uppdaterar en hanterad instans.|
|[Hanterade instanser – Ta bort](/rest/api/sql/managedinstances/delete)|Tar bort en hanterad instans.|
|[Hanterade instanser – Hämta](/rest/api/sql/managedinstances/get)|Hämtar en hanterad instans.|
|[Hanterade instanser – lista](/rest/api/sql/managedinstances/list)|Returnerar en lista över hanterade instanser i en prenumeration.|
|[Hanterade instanser – lista efter resursgrupp](/rest/api/sql/managedinstances/listbyresourcegroup)|Returnerar en lista över hanterade instanser i en resursgrupp.|
|[Hanterade instanser – Uppdatera](/rest/api/sql/managedinstances/update)|Uppdaterar en hanterad instans.|
|[Åtgärder för hanterad instans – lista efter hanterad instans](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Hämtar en lista över hanteringsåtgärder som utförs på den hanterade instansen.|
|[Åtgärder för hanterad instans – Hämta](/rest/api/sql/managedinstanceoperations/get)|Hämtar den specifika hanteringsåtgärd som utförs på den hanterade instansen.|
|[Hanterade instansåtgärder – Avbryt](/rest/api/sql/managedinstanceoperations/cancel)|Avbryter den specifika hanteringsåtgärd som utförs på den hanterade instansen.|

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du migrerar en SQL Server-databas till Azure finns [i Migrera till Azure SQL Database](../database/migrate-to-database-from-sql-server.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](../database/features-comparison.md).