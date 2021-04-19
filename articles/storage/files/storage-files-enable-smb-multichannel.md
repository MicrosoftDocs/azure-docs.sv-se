---
title: Aktivera SMB multichannel
description: Lär dig hur du aktiverar SMB Multichannel på Azure Premium-filresurser.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da4e1a58aef28e5c47100a0311ff81a5af04a918
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718989"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Aktivera SMB Multichannel på ett FileStorage-konto (förhandsversion) 

Azure FileStorage-konton stöder SMB Multichannel (förhandsversion), vilket ökar prestanda från en SMB 3.x-klient genom att upprätta flera nätverksanslutningar till dina premiumfilresurser. Den här artikeln innehåller stegvisa anvisningar för att aktivera SMB Multichannel på ett befintligt lagringskonto. Detaljerad information om hur Azure Files SMB Multichannel finns i SMB Multichannel performance (SMB Multichannel-prestanda).

## <a name="limitations"></a>Begränsningar

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett FileStorage-konto](./storage-how-to-create-file-share.md).
- Om du tänker använda modulen Azure PowerShell installerar [du 3.0.1-förhandsversionen av modulen](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Komma igång

Öppna ett PowerShell-fönster och logga in på din Azure-prenumeration. Därifrån kan du registrera dig för förhandsversionen av SMB Multichannel med följande kommandon.

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

### <a name="verify-that-feature-registration-is-complete"></a>Kontrollera att funktionsregistreringen har slutförts

Eftersom det kan ta upp till en timme att aktivera funktionen på ditt lagringskonto kan du använda följande kommando för att verifiera att den är registrerad för din prenumeration:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Aktivera SMB Multichannel 
När du har skapat ett FileStorage-konto kan du följa anvisningarna för att uppdatera SMB Multichannel-inställningarna för ditt lagringskonto.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Logga in på Azure Portal och gå till det FileStorage-lagringskonto som du vill konfigurera SMB Multichannel på.
1. Välj **Filresurser** under **Filtjänst** och välj sedan **Filresursinställningar.**
1. Växla **SMB Multichannel till** på **(eller** **av för att** inaktivera) och välj **Spara**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Skärmbild av lagringskontot, smb multichannel är toggled på."  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

Om alternativet SMB Multichannel inte visas **under** Filresursinställningar eller om du får ett felmeddelande om att uppdateringsinställningen misslyckades när [](#regional-availability) du uppdaterar konfigurationen kontrollerar du att din prenumeration är registrerad och att ditt konto finns i en av de regioner som stöds med kontotyp och replikering som stöds.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill aktivera SMB Multichannel med Azure PowerShell-modulen måste du installera [3.0.1-förhandsversionen](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) av modulen.

Ange variablerna och `$resourceGroupName` till `$storageAccountName` din resursgrupp och ditt lagringskonto innan du kör dessa PowerShell-kommandon.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI stöder ännu inte konfigurering av SMB Multichannel. Se portalanvisningarna för att konfigurera SMB Multichannel på lagringskontot.

---

> [!NOTE]
> Ändringar i konfigurationsinställningarna för SMB Multichannel gäller för alla filresurser under lagringskontot. Du måste dock återmontera resursen på klienten för att ändringarna ska gälla.


## <a name="next-steps"></a>Nästa steg 

- [Montera om filresursen för](storage-how-to-use-files-windows.md) att dra nytta av SMB Multichannel.
- [Felsök eventuella problem som rör SMB Multichannel](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Mer information om förbättringarna finns i [Prestanda för SMB Multichannel](storage-files-smb-multichannel-performance.md)
 - Mer information om Windows SMB Multichannel-funktionen finns i [Hantera SMB Mulitchannel.](/azure-stack/hci/manage/manage-smb-multichannel)