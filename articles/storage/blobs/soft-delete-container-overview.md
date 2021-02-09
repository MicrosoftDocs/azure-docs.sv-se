---
title: Mjuk borttagning för behållare (förhands granskning)
titleSuffix: Azure Storage
description: Mjuk borttagning för behållare (förhands granskning) skyddar dina data så att du enkelt kan återställa dina data när de är felaktigt ändrade eller borttagna av ett program eller en annan lagrings konto användare.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 0c15be86c282451440f9b81d57f17e835559b5ae
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979115"
---
# <a name="soft-delete-for-containers-preview"></a>Mjuk borttagning för behållare (förhands granskning)

Mjuk borttagning för behållare (för hands version) skyddar dina data från att oavsiktligt eller avsiktligt tas bort. Om mjuk borttagning av behållare är aktiverat för ett lagrings konto behålls alla borttagna behållare och deras innehåll i Azure Storage under den period som du anger. Under kvarhållningsperioden kan du återställa tidigare borttagna behållare. När du återställer en behållare återställs alla blobar i behållaren när den har tagits bort.

För slut punkt till slut punkts skydd för dina BLOB-data rekommenderar Microsoft att du aktiverar följande funktioner för data skydd:

- Behållare för mjuk borttagning, för att återställa en behållare som har tagits bort. Information om hur du aktiverar mjuk borttagning av behållare finns i [Aktivera och hantera mjuk borttagning för behållare](soft-delete-container-enable.md).
- BLOB-versioner för att automatiskt underhålla tidigare versioner av en blob. När BLOB-versioner har Aktiver ATS kan du återställa en tidigare version av en BLOB för att återställa dina data om de felaktigt ändras eller tas bort. Information om hur du aktiverar BLOB-versioner finns i [Aktivera och hantera BLOB-versioner](versioning-enable.md).
- BLOB-mjuk borttagning, för att återställa en BLOB eller version som har tagits bort. Information om hur du aktiverar mjuk borttagning av BLOB finns i [Aktivera och hantera mjuk borttagning för blobbar](soft-delete-blob-enable.md).

> [!WARNING]
> Det går inte att ångra borttagningen av ett lagrings konto. Mjuk borttagning skyddar inte borttagningen av ett lagrings konto, utan endast mot borttagning av data objekt i det kontot. Om du vill skydda ett lagrings konto från borttagning konfigurerar du ett **CannotDelete** -lås på lagrings konto resursen. Mer information om hur du låser Azure Resource Manager-resurser finns i [låsa resurser för att förhindra oväntade ändringar](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>Så här fungerar mjuk borttagning av behållare

När du aktiverar mjuk borttagning av behållare kan du ange en kvarhållningsperiod för borttagna behållare mellan 1 och 365 dagar. Standard kvarhållningsperioden är 7 dagar. Under kvarhållningsperioden kan du återställa en borttagen behållare genom att anropa åtgärden **ta bort behållare** .

När du återställer en behållare återställs även behållarens blobbar och alla BLOB-versioner. Du kan dock endast använda mjuk borttagning av behållare för att återställa blobbar om själva behållaren har tagits bort. Om du vill återställa en borttagen BLOB när dess överordnade behållare inte har tagits bort, måste du använda blob-version med mjuk borttagning eller BLOB-versioner.

Följande diagram visar hur en borttagen behållare kan återställas när mjuk borttagning av behållare är aktiverat:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagram över hur en mjuk, borttagen behållare kan återställas":::

När du återställer en behållare kan du återställa den till dess ursprungliga namn om det namnet inte har använts igen. Om det ursprungliga behållar namnet har använts kan du återställa behållaren med ett nytt namn.

När kvarhållningsperioden har gått ut tas behållaren bort permanent från Azure Storage och kan inte återställas. Klockan börjar på kvarhållningsperioden vid den tidpunkt då behållaren tas bort. Du kan ändra kvarhållningsperioden när som helst, men kom ihåg att en uppdaterad kvarhållningsperiod endast gäller för nyligen borttagna behållare. Tidigare borttagna behållare tas bort permanent utifrån den kvarhållningsperiod som gällde vid den tidpunkt då behållaren togs bort.

Att inaktivera mjuk borttagning av behållare resulterar inte i permanent borttagning av behållare som tidigare varit borttagna. Alla mjuka, borttagna behållare tas bort permanent tills den kvarhållningsperiod som gällde vid den tidpunkt då behållaren togs bort.

## <a name="about-the-preview"></a>Om för hands versionen

Mjuk borttagning av behållare är tillgänglig i för hands versionen i alla Azure-regioner.

> [!IMPORTANT]
> Förhands granskningen av behållarens mjuka borttagning är endast avsedd för användning utan produktion. Service nivå avtal (service avtal) för produktions tjänster är inte tillgängliga för närvarande.

Version 2019-12-12 och högre av Azure Storage REST API stöder mjuk borttagning av behållare.

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
