---
title: Kräv säker överföring för att säkerställa säkra anslutningar
titleSuffix: Azure Storage
description: Lär dig hur du kräver säker överföring för förfrågningar till Azure Storage. När du kräver säker överföring för ett lagrings konto avvisas alla förfrågningar som härstammar från en osäker anslutning.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a0a942daae6b106bce928f8214e92c0c6294203d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555709"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Kräv säker överföring för att säkerställa säkra anslutningar

Du kan konfigurera ditt lagrings konto så att det accepterar begär Anden från säkra anslutningar endast genom att ange egenskapen **säker överföring som krävs** för lagrings kontot. När du kräver säker överföring avvisas alla begär Anden som härstammar från en osäker anslutning. Microsoft rekommenderar att du alltid behöver säker överföring för alla dina lagrings konton.

När en säker överföring krävs måste ett anrop till en Azure Storage REST API-åtgärd göras via HTTPS. Alla begär Anden som görs över HTTP nekas.

Det går inte att ansluta till en Azure-filresurs via SMB utan kryptering när en säker överföring krävs för lagrings kontot. Exempel på oskyddade anslutningar är de som görs via SMB 2,1, SMB 3,0 utan kryptering eller vissa versioner av Linux SMB-klienten.

Som standard aktive ras egenskapen för **säker överföring som krävs** när du skapar ett lagrings konto.

> [!NOTE]
> Eftersom Azure Storage inte stöder HTTPS för anpassade domän namn, tillämpas inte det här alternativet när du använder ett anpassat domän namn. Och klassiska lagrings konton stöds inte.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Kräv säker överföring i Azure Portal

Du kan aktivera egenskapen för **säker överföring som krävs** när du skapar ett lagrings konto i [Azure Portal](https://portal.azure.com). Du kan också aktivera den för befintliga lagrings konton.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Kräv säker överföring för ett nytt lagrings konto

1. Öppna fönstret **skapa lagrings konto** i Azure Portal.
1. Under **säker överföring krävs** väljer du **aktive rad**.

   ![Bladet skapa lagrings konto](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Kräv säker överföring för ett befintligt lagrings konto

1. Välj ett befintligt lagrings konto i Azure Portal.
1. I meny fönstret lagrings konto under **Inställningar** väljer du **konfiguration**.
1. Under **säker överföring krävs** väljer du **aktive rad**.

   ![Meny fönstret lagrings konto](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Kräv säker överföring från kod

Om du vill kräva säker överföring program mässigt ställer du in egenskapen _enableHttpsTrafficOnly_ på _True_ på lagrings kontot. Du kan ställa in den här egenskapen med hjälp av providern för lagrings resurser REST API, klient bibliotek eller verktyg:

* [REST-API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/@azure/arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Kräv säker överföring med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Det här exemplet kräver Azure PowerShell-modulen AZ version 0,7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

Kör `Connect-AzAccount` för att skapa en anslutning med Azure.

 Använd följande kommando rad för att kontrol lera inställningen:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Använd följande kommando rad för att aktivera inställningen:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Kräv säker överföring med Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Använd följande kommando för att kontrol lera inställningen:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Använd följande kommando för att aktivera inställningen:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Nästa steg

[Säkerhets rekommendationer för Blob Storage](../blobs/security-recommendations.md)
