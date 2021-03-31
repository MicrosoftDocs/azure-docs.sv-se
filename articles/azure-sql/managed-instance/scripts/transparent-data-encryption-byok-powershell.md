---
title: 'PowerShell: Aktivera TDE (BYOK)'
titleSuffix: Azure SQL Managed Instance
description: Lär dig hur du konfigurerar den hanterade Azure SQL-instansen att börja använda BYOK-transparent datakryptering (TDE) för kryptering vid vila med hjälp av PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323249"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>transparent datakryptering i SQL-hanterad instans med hjälp av din egen nyckel från Azure Key Vault

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Detta exempel på PowerShell-skript konfigurerar transparent datakryptering (TDE) med en kundhanterad nyckel för Azure SQL-hanterad instans med hjälp av en nyckel från Azure Key Vault. Detta kallas ofta BYOK-scenario () för TDE. Läs mer i [Azure SQL Transparent datakryptering med kundhanterad nyckel](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- En befintlig hanterad instans. Se [använda PowerShell för att skapa en hanterad instans](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Att använda PowerShell lokalt eller med hjälp av Azure Cloud Shell kräver Azure PowerShell 2.3.2 eller en senare version. Om du behöver uppgradera kan du läsa [installera Azure PowerShell modul](/powershell/azure/install-az-ps)eller köra exempel skriptet nedan för att installera modulen för den aktuella användaren:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell dokumentation](/powershell/azure/).

Ytterligare PowerShell-skript exempel för SQL-hanterad instans finns i [PowerShell-skript för Azure SQL-hanterad](../../database/powershell-script-content-guide.md)instans.
