---
title: Mjuk borttagning för behållare (förhands granskning)
titleSuffix: Azure Storage
description: Mjuk borttagning för behållare (förhands granskning) skyddar dina data så att du enkelt kan återställa dina data när de är felaktigt ändrade eller borttagna av ett program eller en annan lagrings konto användare.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 54e703b096ea4e3572a6fc00aa6b7b2b99c4bcad
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800752"
---
# <a name="soft-delete-for-containers-preview"></a>Mjuk borttagning för behållare (förhands granskning)

Mjuk borttagning för behållare (för hands version) skyddar dina data från att oavsiktligt eller avsiktligt tas bort. När mjuk borttagning av containrar är aktiverat för ett lagringskonto behålls borttagna containrar och deras innehåll i Azure Storage under den period som du anger. Under kvarhållningsperioden kan du återställa tidigare borttagna containrar. När du återställer en container återställs alla blobar som fanns i den containern när den togs bort.

För slut punkt till slut punkts skydd för dina BLOB-data rekommenderar Microsoft att du aktiverar följande funktioner för data skydd:

- Behållare för mjuk borttagning, för att återställa en behållare som har tagits bort. Information om hur du aktiverar mjuk borttagning av behållare finns i [Aktivera och hantera mjuk borttagning för behållare](soft-delete-container-enable.md).
- BLOB-versioner för att automatiskt underhålla tidigare versioner av en blob. När BLOB-versioner har Aktiver ATS kan du återställa en tidigare version av en BLOB för att återställa dina data om de felaktigt ändras eller tas bort. Information om hur du aktiverar BLOB-versioner finns i [Aktivera och hantera BLOB-versioner](versioning-enable.md).
- BLOB-mjuk borttagning, för att återställa en BLOB eller version som har tagits bort. Information om hur du aktiverar mjuk borttagning av BLOB finns i [Aktivera och hantera mjuk borttagning för blobbar](soft-delete-blob-enable.md).

> [!IMPORTANT]
> Mjuk borttagning av behållare är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.

## <a name="how-container-soft-delete-works"></a>Så här fungerar mjuk borttagning av behållare

När du aktiverar mjuk borttagning av behållare kan du ange en kvarhållningsperiod för borttagna behållare mellan 1 och 365 dagar. Standard kvarhållningsperioden är 7 dagar. Under kvarhållningsperioden kan du återställa en borttagen behållare genom att anropa åtgärden för att **återställa behållare** .

När du återställer en behållare återställs även behållarens blobbar och alla BLOB-versioner. Du kan dock endast använda mjuk borttagning av behållare för att återställa blobbar om själva behållaren har tagits bort. Om du vill återställa en borttagen BLOB när dess överordnade behållare inte har tagits bort, måste du använda blob-version med mjuk borttagning eller BLOB-versioner.

> [!WARNING]
> Behållarens mjuka borttagning kan bara återställa hela behållare och de blobbar de hade vid tidpunkten för borttagning. Det går inte att återställa en borttagen BLOB i en behållare med hjälp av mjuk borttagning av behållare.

Följande diagram visar hur en borttagen behållare kan återställas när mjuk borttagning av behållare är aktiverat:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagram över hur en mjuk, borttagen behållare kan återställas":::

När du återställer en behållare kan du återställa den till dess ursprungliga namn om det namnet inte har använts igen. Om det ursprungliga behållar namnet har använts kan du återställa behållaren med ett nytt namn.

När kvarhållningsperioden har gått ut tas behållaren bort permanent från Azure Storage och kan inte återställas. Klockan börjar på kvarhållningsperioden vid den tidpunkt då behållaren tas bort. Du kan ändra kvarhållningsperioden när som helst, men kom ihåg att en uppdaterad kvarhållningsperiod endast gäller för nyligen borttagna behållare. Tidigare borttagna behållare tas bort permanent utifrån den kvarhållningsperiod som gällde vid den tidpunkt då behållaren togs bort.

Att inaktivera mjuk borttagning av behållare resulterar inte i permanent borttagning av behållare som tidigare varit borttagna. Alla mjuka, borttagna behållare tas bort permanent tills den kvarhållningsperiod som gällde vid den tidpunkt då behållaren togs bort.

> [!IMPORTANT]
> Mjuk borttagning av behållare skyddar inte borttagningen av ett lagrings konto, utan endast mot borttagning av behållare i det kontot. Om du vill skydda ett lagrings konto från borttagning konfigurerar du ett lås på lagrings konto resursen. Mer information om hur du låser Azure Resource Manager-resurser finns i [låsa resurser för att förhindra oväntade ändringar](../../azure-resource-manager/management/lock-resources.md).

## <a name="about-the-preview"></a>Om för hands versionen

Mjuk borttagning av behållare är tillgänglig i för hands versionen i alla Azure-regioner.

Version 2019-12-12 eller senare av Azure Storage REST API stöder mjuk borttagning av behållare.

### <a name="storage-account-support"></a>Stöd för lagrings konto

Den mjuka borttagningen av behållare är tillgänglig för följande typer av lagrings konton:

- Generell-syfte v2 och v1 lagrings konton
- Blockera Blob Storage-konton
- Blob Storage-konton

Lagrings konton med hierarkiskt namn område som är aktiverade för användning med Azure Data Lake Storage Gen2 stöds också.

### <a name="register-for-the-preview"></a>Registrera dig för för hands versionen

Om du vill registrera dig för för hands versionen av container Soft Delete använder du PowerShell eller Azure CLI för att skicka en begäran om att registrera funktionen med din prenumeration. När din begäran har godkänts kan du aktivera mjuk borttagning av behållare med eventuella nya eller befintliga General-Purpose v2-, Blob Storage-och Premium Block-Blob Storage-konton.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrera med PowerShell genom att anropa kommandot [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera dig med Azure CLI anropar du kommandot [AZ funktions register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Kontrol lera status för registreringen

Använd PowerShell eller Azure CLI för att kontrol lera status för registreringen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrol lera status för registreringen med PowerShell anropar du kommandot [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrol lera statusen för registreringen med Azure CLI anropar du kommandot [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Priser och fakturering

Det finns ingen ytterligare avgift för att aktivera mjuk borttagning av behållare. Data i Soft Deleted-behållare faktureras enligt samma taxa som aktiva data.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera mjuk borttagning av behållare](soft-delete-container-enable.md)
- [Mjuk borttagning för blobar](soft-delete-blob-overview.md)
- [BLOB-versioner](versioning-overview.md)
