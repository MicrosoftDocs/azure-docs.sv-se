---
title: Aktivera SMB Multichannel
description: Lär dig hur du aktiverar SMB Multichannel på Azure Premium-filresurser.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732552"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Aktivera SMB Multichannel på ett FileStorage-konto (förhands granskning) 

Azure FileStorage-konton stöder SMB Multichannel (för hands version), vilket ökar prestandan från en SMB 3. x-klient genom att upprätta flera nätverks anslutningar till dina Premium-filresurser. Den här artikeln innehåller steg-för-steg-anvisningar om hur du aktiverar SMB Multichannel på ett befintligt lagrings konto. Detaljerad information om Azure Files SMB Multichannel finns i SMB Multichannel-prestanda.

## <a name="limitations"></a>Begränsningar

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett FileStorage-konto](./storage-how-to-create-file-share.md).
- Om du tänker använda Azure PowerShell-modulen installerar du [3.0.1-Preview-versionen av modulen](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Komma igång

Öppna ett PowerShell-fönster och logga in i din Azure-prenumeration. Därifrån kan du registrera dig för för hands versionen av SMB Multichannel med följande kommandon.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> Registreringen kan ta upp till en timme.

### <a name="verify-that-feature-registration-is-complete"></a>Verifiera att funktions registreringen har slutförts

Eftersom det kan ta upp till en timme att aktivera funktionen på ditt lagrings konto kan du använda följande kommando för att kontrol lera att den är registrerad för din prenumeration:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Aktivera SMB Multichannel 
När du har skapat ett FileStorage-konto kan du följa anvisningarna för att uppdatera SMB Multichannel-inställningar för ditt lagrings konto.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Logga in på Azure Portal och navigera till det FileStorage lagrings konto som du vill konfigurera SMB Multichannel på.
1. Välj **fil resurser** under **fil tjänst** och välj sedan **fil resurs inställningar**.
1. Växla **SMB Multichannel** till **på** (eller **inaktivera** ) och välj **Spara**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Skärm bild av lagrings kontot, SMB Multichannel har växlats på.":::

Om alternativet SMB Multichannel inte är synligt under **Inställningar för fildelning** eller om du får ett fel vid uppdatering av konfigurationen, kontrollerar du att din prenumeration har registrerats och att ditt konto är i en av de [regioner som stöds](#regional-availability) och att det finns stöd för konto typ och replikering.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill aktivera SMB Multichannel med hjälp av Azure PowerShell-modulen måste du [Installera 3.0.1-Preview-versionen](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) av modulen.

Ange variablerna `$resourceGroupName` och `$storageAccountName` till din resurs grupp och ditt lagrings konto innan du kör dessa PowerShell-kommandon.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI har ännu inte stöd för konfiguration av SMB Multichannel. Se Portal anvisningarna för att konfigurera SMB Multichannel på lagrings konto.

---

> [!NOTE]
> Eventuella ändringar av konfigurations inställningarna för SMB Multichannel kommer att gälla alla fil resurser under lagrings kontot. Du måste montera om resursen på klienten för att ändringarna ska börja gälla.


## <a name="next-steps"></a>Nästa steg 

- [Montera om fil resursen](storage-how-to-use-files-windows.md) för att dra nytta av SMB Multichannel.
- [Felsök eventuella problem som du har relaterat till SMB Multichannel](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Läs mer om förbättringarna i [SMB Multichannel-prestanda](storage-files-smb-multichannel-performance.md)
 - Mer information om funktionen Windows SMB Multichannel finns i [Hantera SMB-Multichannel](/azure-stack/hci/manage/manage-smb-multichannel).