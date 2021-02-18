---
title: Hantera standard åtkomst nivån för ett Azure Storage konto
description: Lär dig hur du ändrar standard åtkomst nivån för ett GPv2-eller Blob Storage-konto
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 637f748882b3ac84127c8b71761a06629e1e0957
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653840"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Hantera standard åtkomst nivån för ett Azure Storage konto

Varje Azure Storage konto har en standard åtkomst nivå, antingen frekvent eller låg frekvent. Du tilldelar åtkomst nivån när du skapar ett lagrings konto. Standard åtkomst nivån är aktiv.

Du kan ändra standard konto nivån genom att ange attributet **åtkomst nivå** för lagrings kontot. Ändring av konto nivån gäller för alla objekt som lagras i kontot som inte har någon explicit nivå uppsättning. När konto nivån växlas från frekvent till låg frekvent tillkommer Skriv åtgärder (per 10 000) för alla blobbar utan en uppsättnings nivå i GPv2-konton och växling från låg frekvent till frekventt medför både Läs åtgärder (per 10 000) och data hämtning (per GB) avgifter för alla blobbar i Blob Storage-och GPv2-konton.

För blobbar med nivån angiven på objekt nivå gäller inte konto nivån. Nivån arkivlagring kan endast anges på objektnivå. Du kan när som helst växla mellan åtkomst nivåer.

Du kan ändra standard åtkomst nivån när ett lagrings konto har skapats genom att följa stegen nedan.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändra standard konto åtkomst nivån för ett GPv2-eller Blob Storage konto

Följande scenarier använder Azure Portal eller PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj **alla resurser** i Azure Portal.

1. Välj ditt lagringskonto.

1. I **Inställningar** väljer du **konfiguration** för att visa och ändra konto konfigurationen.

1. Välj rätt åtkomst nivå för dina behov: ange **åtkomst nivå** till antingen **cool** eller **hett**.

1. Klicka på **Spara** högst upp.

![Ändra standard konto nivå i Azure Portal](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Följande PowerShell-skript kan användas för att ändra konto nivån. `$rgName`Variabeln måste initieras med resurs gruppens namn. `$accountName`Variabeln måste initieras med ditt lagrings konto namn.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Nästa steg

- [Hantera nivån för en BLOB i ett Azure Storage konto](../blobs/manage-access-tier.md)
- [Ta reda på om Premium prestanda skulle dra nytta av din app](../blobs/storage-blob-performance-tiers.md)
- [Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../blobs/monitor-blob-storage.md)
