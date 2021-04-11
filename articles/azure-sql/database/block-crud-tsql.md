---
title: Blockera T-SQL-kommandon för att skapa eller ändra Azure SQL-resurser
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: Den här artikeln beskriver en funktion som gör att Azure-administratörer kan blockera T-SQL-kommandon för att skapa eller ändra Azure SQL-resurser
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556201"
---
# <a name="what-is-block-t-sql-crud-feature"></a>Vad är funktionen blockera T-SQL-CRUD?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Med den här funktionen kan Azure-administratörer blockera skapandet eller ändringen av Azure SQL-resurser via T-SQL. Detta tillämpas på prenumerations nivå för att blockera T-SQL-kommandon från att påverka SQL-resurser i en Azure SQL-databas eller en hanterad instans.

## <a name="overview"></a>Översikt

Om du vill blockera skapandet eller ändringen av resurser via T-SQL och framtvinga resurs hantering via en Azure Resource Manager-mall (ARM-mall) för en specifik prenumeration kan du använda funktionerna för för hands versionen av prenumerations nivå i Azure Portal. Detta är särskilt användbart när du använder [Azure-principer](/azure/governance/policy/overview) för att genomdriva organisations standarder med ARM-mallar. Eftersom T-SQL inte följer Azure-principerna kan ett block på T-SQL skapa eller ändra-åtgärder tillämpas. Den blockerade syntaxen innehåller CRUD-uttryck (Create, Update, Delete) för databaser i Azure SQL, specifikt `CREATE DATABASE` , `ALTER DATABASE` och- `DROP DATABASE` instruktioner. 

T-SQL CRUD-åtgärder kan blockeras via Azure Portal, [PowerShell](/powershell/module/az.resources/register-azproviderfeature)eller [Azure CLI](/cli/azure/feature#az_feature_register).

## <a name="permissions"></a>Behörigheter

För att kunna registrera eller ta bort den här funktionen måste Azure-användaren vara medlem i Prenumerationens ägare eller deltagar roll.

## <a name="examples"></a>Exempel

I följande avsnitt beskrivs hur du kan registrera eller avregistrera en förhands gransknings funktion med Microsoft. SQL Resource Provider i Azure Portal: 

### <a name="register-block-t-sql-crud"></a>Registrera blockera T-SQL-CRUD

1. Gå till din prenumeration på Azure Portal.
2. Välj fliken för **hands versions funktioner** . 
3. Välj **blockera T-SQL-CRUD**.
4. När du har valt i **blockera T-SQL-CRUD** öppnas ett nytt fönster, Välj **Registrera**, för att registrera det här blocket med Microsoft. SQL Resource Provider.

![Välj "blockera T-SQL-CRUD" i listan över för hands versions funktioner](./media/block-tsql-crud/block-tsql-crud.png)

![Med alternativet "blockera T-SQL-CRUD" markerat väljer du registrera](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Registrera Microsoft. SQL Resource Provider igen 
När du har registrerat blocket för T-SQL-CRUD med Microsoft. SQL Resource Provider måste du registrera om Microsoft. SQL Resource Provider för att ändringarna ska börja gälla. Registrera Microsoft. SQL-resurs leverantören på nytt:

1. Gå till din prenumeration på Azure Portal.
2. Välj fliken **resurs leverantörer** .
3. Sök och välj **Microsoft. SQL** Resource Provider.
4. Välj **registrera igen**. 

> [!NOTE]
> Omregistrerings steget är obligatoriskt för T-SQL-blocket som ska tillämpas på din prenumeration. 

![Registrera Microsoft. SQL-resurs leverantören igen](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>Tar bort block T-SQL-CRUD
Om du vill ta bort blocket på T-SQL skapa eller ändra åtgärder från din prenumeration måste du först avregistrera det tidigare registrerade T-SQL-blocket. Registrera sedan om Microsoft. SQL-resurs leverantören som visas ovan för borttagning av T-SQL-blocket för att börja gälla. 


## <a name="next-steps"></a>Nästa steg

- [En översikt över Azure SQL Database säkerhets funktioner](security-overview.md)
- [Rekommenderade säkerhets metoder för Azure SQL Database](security-best-practice.md)
