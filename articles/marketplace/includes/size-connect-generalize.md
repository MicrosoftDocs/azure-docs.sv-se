---
title: ta med fil
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: addc18a0ebf9e49d3474d3f40cb1e2a6e0f0b272
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826627"
---
## <a name="generalize-the-image"></a>Generalisera avbildningen

Alla avbildningar på Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå detta måste den virtuella hård disken generaliseras, en åtgärd som tar bort alla instans-/regionsspecifika identifierare och program driv rutiner från en virtuell dator.

### <a name="for-windows"></a>För Windows

Windows OS-diskar generaliseras med [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) -verktyget. Om du senare uppdaterar eller omkonfigurerar operativ systemet måste du köra Sysprep igen.

> [!WARNING]
> När du har kört Sysprep stänger du av den virtuella datorn tills den har distribuerats eftersom uppdateringar kan köras automatiskt. Den här avstängningen kommer att undvika efterföljande uppdateringar från att göra instansbaserade ändringar i operativ systemet eller de installerade tjänsterna. Mer information om hur du kör Sysprep finns i [steg för att generalisera en virtuell hård disk](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>För Linux

Följande process generaliserar en virtuell Linux-dator och distribuerar den igen som en separat virtuell dator. Mer information finns i [så här skapar du en avbildning av en virtuell dator eller virtuell hård disk](../../virtual-machines/linux/capture-image.md). Du kan stoppa när du når avsnittet "skapa en virtuell dator från avbildningen".

1. Ta bort Azure Linux-agenten.
    1. Anslut till din virtuella Linux-dator med hjälp av en SSH-klient.
    2. I SSH-fönstret anger du följande kommando: `sudo waagent –deprovision+user` .
    3. Skriv in Y om du vill fortsätta (du kan lägga till parametern-Force i föregående kommando för att undvika bekräftelse steget).
    4. När kommandot har slutförts anger du **Avsluta** för att stänga SSH-klienten.
2. Stoppa virtuell dator.
    1. I Azure Portal väljer du resurs gruppen (RG) och avallokerar den virtuella datorn.
    2. Den virtuella datorn är nu generaliserad och du kan skapa en ny virtuell dator med den här virtuella dator disken.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Ta en ögonblicks bild av den virtuella dator disken

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/).
2. Börja längst upp till vänster, Välj **skapa en resurs** och Sök sedan efter och välj **ögonblicks bild**.
3. På bladet ögonblicks bild väljer du  **skapa**.
4. Ange ett **namn** för ögonblicks bilden.
5. Välj en befintlig resurs grupp eller ange ett namn för en ny resurs grupp.
6. För **käll disk** väljer du den hanterade disk som ska avbildas.
7. Välj den **Kontotyp** som ska användas för att lagra ögonblicks bilden. Använd **standard HDD** om du inte behöver den lagrad på ett högt presterande SSD.
8. Välj **Skapa**.

#### <a name="extract-the-vhd"></a>Extrahera den virtuella hård disken

Använd följande skript för att exportera ögonblicks bilden till en virtuell hård disk i ditt lagrings konto.

```azurecli-interactive
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att generera SAS-URI för en ögonblicks bild och kopierar den underliggande virtuella hård disken till ett lagrings konto med SAS-URI: n. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
| --- | --- |
| az disk grant-access | Genererar skrivskyddad SAS som används för att kopiera den underliggande VHD-filen till ett lagringskonto eller för att ladda ned den till en lokal plats
| az storage blob copy start | Kopierar en BLOB asynkront från ett lagrings konto till ett annat. Används `az storage blob show` för att kontrol lera status för den nya blobben. |
|
