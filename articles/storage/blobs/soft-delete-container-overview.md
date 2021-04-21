---
title: Mjuk borttagning för containrar (förhandsversion)
titleSuffix: Azure Storage
description: Mjuk borttagning för containrar (förhandsversion) skyddar dina data så att du enklare kan återställa dina data när de har ändrats eller tagits bort felaktigt av ett program eller av en annan lagringskontoanvändare.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 2efd673d26231e83d820f7971a740d06e9b2a1d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785423"
---
# <a name="soft-delete-for-containers-preview"></a>Mjuk borttagning för containrar (förhandsversion)

Mjuk borttagning för containrar (förhandsversion) skyddar dina data från att tas bort av misstag eller på ett skadligt sätt. När mjuk borttagning av containrar har aktiverats för ett lagringskonto bevaras en borttagna container och dess innehåll i Azure Storage den period som du anger. Under kvarhållningsperioden kan du återställa tidigare borttagna containrar. När du återställer en container återställs alla blobar som fanns i den containern när den togs bort.

För att skydda dina blobdata från slut till slut rekommenderar Microsoft att du aktiverar följande dataskyddsfunktioner:

- Mjuk borttagning av containrar för att återställa en container som har tagits bort. Information om hur du aktiverar mjuk borttagning av containrar finns i [Aktivera och hantera mjuk borttagning för containrar.](soft-delete-container-enable.md)
- Versionshantering av blobar för att automatiskt underhålla tidigare versioner av en blob. När versionshantering av blobar är aktiverat kan du återställa en tidigare version av en blob för att återställa dina data om de har ändrats eller tagits bort felaktigt. Information om hur du aktiverar versionshantering av blobar finns [i Aktivera och hantera blobversionshantering.](versioning-enable.md)
- Mjuk borttagning av blob för att återställa en blob eller version som har tagits bort. Information om hur du aktiverar mjuk borttagning av blobar finns [i Aktivera och hantera mjuk borttagning för blobar.](soft-delete-blob-enable.md)

> [!IMPORTANT]
> Mjuk borttagning av containrar finns för närvarande i **FÖRHANDSVERSION.** Se kompletterande [användningsvillkor för förhandsversioner Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som ännu inte har släppts i allmän tillgänglighet.

## <a name="how-container-soft-delete-works"></a>Så här fungerar mjuk borttagning av containrar

När du aktiverar mjuk borttagning av containrar kan du ange en kvarhållningsperiod för borttagna containrar som är mellan 1 och 365 dagar. Standardperioden för kvarhållning är 7 dagar. Under kvarhållningsperioden kan du återställa en borttagna container genom att anropa åtgärden **Återställ container.**

När du återställer en container återställs även containerns blobar och eventuella blobversioner. Du kan dock bara använda mjuk borttagning av containrar för att återställa blobar om själva containern har tagits bort. Om du vill återställa en borttagna blob när dess överordnade container inte har tagits bort måste du använda versionshantering för mjuk borttagning av blobar eller blobar.

> [!WARNING]
> Mjuk borttagning av containrar kan bara återställa hela containrar och deras innehåll vid tidpunkten för borttagningen. Du kan inte återställa en borttagna blob i en container med hjälp av mjuk borttagning av containrar. Microsoft rekommenderar också att du aktiverar versionshantering för mjuk borttagning av blobar och blobar för att skydda enskilda blobar i en container.

Följande diagram visar hur en borttagna container kan återställas när mjuk borttagning av containrar är aktiverat:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagram som visar hur en mjukt borttagna container kan återställas":::

När du återställer en container kan du återställa den till dess ursprungliga namn om namnet inte har återanvändas. Om det ursprungliga containernamnet har använts kan du återställa den med ett nytt namn.

När kvarhållningsperioden har gått ut tas containern bort permanent från Azure Storage kan inte återställas. Klockan börjar på kvarhållningsperioden vid den tidpunkt då containern tas bort. Du kan ändra kvarhållningsperioden när som helst, men tänk på att en uppdaterad kvarhållningsperiod endast gäller för nyligen borttagna containrar. Tidigare borttagna containrar tas bort permanent baserat på den kvarhållningsperiod som var i kraft när containern togs bort.

Inaktivering av mjuk borttagning av containrar leder inte till permanent borttagning av containrar som tidigare har mjuk borttagning. Eventuella mjukt borttagna containrar tas bort permanent när kvarhållningsperioden som gäller när containern togs bort upphörde.

> [!IMPORTANT]
> Mjuk borttagning av containrar skyddar inte mot borttagning av ett lagringskonto. Den skyddar endast mot borttagning av containrar i det kontot. Om du vill skydda ett lagringskonto från borttagning konfigurerar du ett lås för lagringskontoresursen. Mer information om hur du låser ett lagringskonto finns [i Tillämpa ett Azure Resource Manager för ett lagringskonto.](../common/lock-account-resource.md)

## <a name="about-the-preview"></a>Om förhandsversionen

Mjuk borttagning av containrar är tillgängligt som förhandsversion i alla Azure-regioner.

Version 2019-12-12 eller senare av Azure Storage REST API har stöd för mjuk borttagning av containrar.

### <a name="storage-account-support"></a>Stöd för lagringskonto

Mjuk borttagning av containrar är tillgängligt för följande typer av lagringskonton:

- Lagringskonton för generell användning v2 och v1
- Blockbloblagringskonton
- Blob Storage-konton

Lagringskonton med en hierarkisk namnrymd aktiverad för användning med Azure Data Lake Storage Gen2 stöds också.

### <a name="register-for-the-preview"></a>Registrera dig för förhandsversionen

Om du vill registrera dig i förhandsversionen för mjuk borttagning av containrar använder du PowerShell eller Azure CLI för att skicka en begäran om att registrera funktionen i din prenumeration. När din begäran har godkänts kan du aktivera mjuk borttagning av containrar med alla nya eller befintliga konton för generell användning v2, Blob Storage eller Premium-blockbloblagring.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill registrera dig med PowerShell anropar du kommandot [Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera dig med Azure CLI anropar [du kommandot az feature register.](/cli/azure/feature#az_feature_register)

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Kontrollera statusen för din registrering

Om du vill kontrollera statusen för registreringen använder du PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrollera statusen för din registrering med PowerShell anropar du kommandot [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrollera statusen för din registrering med Azure CLI anropar du [kommandot az feature.](/cli/azure/feature#az_feature_show)

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Priser och fakturering

Det tillkommer inga extra avgifter för att aktivera mjuk borttagning av containrar. Data i mjukt borttagna containrar debiteras med samma hastighet som aktiva data.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera mjuk borttagning av container](soft-delete-container-enable.md)
- [Mjuk borttagning för blobar](soft-delete-blob-overview.md)
- [Versionshantering av blobar](versioning-overview.md)
