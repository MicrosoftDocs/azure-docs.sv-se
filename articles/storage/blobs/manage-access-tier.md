---
title: Hantera åtkomst nivån för en BLOB i ett Azure Storage konto
description: Lär dig hur du ändrar nivån för en BLOB i ett GPv2-eller Blob Storage konto
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166516"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Hantera åtkomst nivån för en BLOB i ett Azure Storage konto

Varje Blob har en standard åtkomst nivå, antingen frekvent, låg frekvent eller arkiverad. En BLOB använder standard åtkomst nivån för det Azure Storage konto där den skapas. Blob Storage-och GPv2-konton exponerar attributet **åtkomst nivå** på konto nivå. Det här attributet anger standard åtkomst nivån för alla blobar som inte uttryckligen anges på objekt nivå. För objekt med nivån angiven på objekt nivå gäller inte konto nivån. Arkiv nivån kan endast tillämpas på objekt nivå. Du kan växla mellan åtkomst nivåer när du vill genom att följa stegen nedan.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändra nivån för en BLOB i ett GPv2-eller Blob Storage konto

Följande scenarier använder Azure Portal eller PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj **alla resurser** i Azure Portal.

1. Välj ditt lagringskonto.

1. Välj din behållare och välj sedan din BLOB.

1. I **BLOB-egenskaperna** väljer du **ändra nivå**.

1. Välj åtkomst nivån frekvent  **, låg** frekvent eller **Arkiv** . Om din BLOB för närvarande finns i arkivet och du vill rehydratisera till en onlinenivå, kan du också välja en rehydratiserad prioritet för **standard** eller **hög**.

1. Välj **Spara** längst ned.

![Ändra BLOB-nivå i Azure Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Följande PowerShell-skript kan användas för att ändra BLOB-nivån. `$rgName`Variabeln måste initieras med resurs gruppens namn. `$accountName`Variabeln måste initieras med ditt lagrings konto namn. `$containerName`Variabeln måste initieras med ditt container namn. `$blobName`Variabeln måste initieras med ditt BLOB-namn.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Nästa steg

- [Hantera standard konto åtkomst nivån för ett Azure Storage-konto](../common/manage-account-default-access-tier.md)
- [Lär dig mer om återuppväcks BLOB-data från Arkiv lag rings nivån](storage-blob-rehydration.md)
- [Kontrol lera priser för frekvent, låg frekvent och Arkiv lag ring i Blob Storage-och GPv2-konton per region](https://azure.microsoft.com/pricing/details/storage/)
