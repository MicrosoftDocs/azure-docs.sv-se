---
title: Generera en SAS-URI för en VM-avbildning – Azure Marketplace
description: Generera en URL för signatur för delad åtkomst (SAS) för en virtuell hård disk (VHD) på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 21ccafe3e15f902e35657a9aa31516bbaeb3b4c8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558014"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Så här skapar du en SAS-URI för en VM-avbildning

> [!NOTE]
> Du behöver inte en SAS-URI för att publicera den virtuella datorn. Du kan helt enkelt dela en bild i delar av centret. Se [skapa en virtuell dator med en godkänd bas](./azure-vm-create-using-approved-base.md) eller [skapa en virtuell dator med hjälp av dina egna avbildnings](./azure-vm-create-using-own-image.md) instruktioner.

Att skapa SAS-URI: er för dina virtuella hård diskar uppfyller följande krav:

- De stöder bara ohanterade virtuella hård diskar.
- Endast list-och Läs behörigheter krävs. Ange inte Skriv-eller borttagnings behörighet.
- Tiden för åtkomst (utgångs datum) bör vara minst tre veckor från det att SAS-URI: n skapas.
- För att skydda mot UTC-tidsändringar anger du Start datumet till en dag före det aktuella datumet. Om det aktuella datumet är till exempel 16 juni 2020 väljer du 6/15/2020.

## <a name="extract-vhd-from-a-vm"></a>Extrahera VHD från en virtuell dator

> [!NOTE]
> Du kan hoppa över det här steget om du redan har en virtuell hård disk som laddats upp i ett lagrings konto.

Om du vill extrahera den virtuella hård disken från den virtuella datorn måste du ta en ögonblicks bild av din virtuella dator disk och extrahera VHD från ögonblicks bilden.

Starta genom att ta en ögonblicks bild av den virtuella dator disken:

1. Logga in på Azure-portalen.
2. Börja längst upp till vänster, Välj Skapa en resurs och Sök sedan efter och välj ögonblicks bild.
3. På bladet ögonblicks bild väljer du skapa.
4. Ange ett namn för ögonblicks bilden.
5. Välj en befintlig resurs grupp eller ange ett namn för en ny resurs grupp.
6. För käll disk väljer du den hanterade disk som ska avbildas.
7. Välj den kontotyp som ska användas för att lagra ögonblicks bilden. Använd Standard HDD om du inte behöver den lagrad på ett högt presterande SSD.
8. Välj Skapa.

### <a name="extract-the-vhd"></a>Extrahera den virtuella hård disken

Använd följande skript för att exportera ögonblicks bilden till en virtuell hård disk i ditt lagrings konto.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. Currently, only general purpose v1 storage is supported.
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

$sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att generera SAS-URI för en ögonblicks bild och kopierar den underliggande virtuella hård disken till ett lagrings konto med SAS-URI: n. Varje kommando i tabellen länkar till kommandospecifik dokumentation.


|Kommando  |Kommentarer  |
|---------|---------|
| az disk grant-access    |     Genererar skrivskyddad SAS som används för att kopiera den underliggande VHD-filen till ett lagringskonto eller för att ladda ned den till en lokal plats    |
|  az storage blob copy start   |    Kopierar en BLOB asynkront från ett lagrings konto till ett annat. Använd AZ Storage BLOB show för att kontrol lera status för den nya blobben.     |
|

## <a name="generate-the-sas-address"></a>Generera SAS-adressen

Det finns två vanliga verktyg som används för att skapa en SAS-adress (URL):

1. **Azure Storage Explorer** – finns på Azure Portal.
2. **Azure CLI** – rekommenderas för operativ system som inte kommer från Windows och automatiserade eller kontinuerliga integrerings miljöer.

### <a name="using-tool-1-azure-storage-explorer"></a>Använda verktyg 1: Azure Storage Explorer

1. Gå till ditt **lagrings konto**.
1. Öppna **Storage Explorer**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Fönstret lagrings konto.":::

3. I **behållaren** högerklickar du på VHD-filen och väljer **Hämta signatur för resurs åtkomst**.
4. I dialog rutan **signatur för delad åtkomst** fyller du i följande fält:

    1. Start tid – behörighetens start datum för VHD-åtkomst. Ange ett datum som infaller en dag före det aktuella datumet.
    2. Förfallo tid – behörighetens förfallo datum för VHD-åtkomst. Ange ett datum minst tre veckor bortom det aktuella datumet.
    3. Behörigheter – Välj behörigheterna läsa och lista.
    4. Container-Level – Markera kryss rutan skapa signatur-URI för delad åtkomst på behållare nivå.

    ![Dialog rutan signatur för delad åtkomst.](media/vm/create-sas-uri-storage-explorer.png)

5. Om du vill skapa en tillhör ande SAS-URI för den här virtuella hård disken väljer du **skapa**.
6. Kopiera URI: n och spara den i en textfil på en säker plats. Den här genererade SAS-URI: n är för åtkomst på container nivå. Om du vill göra det speciellt kan du redigera text filen och lägga till VHD-namnet.
7. Infoga ditt VHD-namn efter VHD-strängen i SAS-URI: n (inklusive ett snedstreck). Den sista SAS-URI: n bör se ut så här:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Upprepa de här stegen för varje virtuell hård disk i de planer som du ska publicera.

### <a name="using-tool-2-azure-cli"></a>Använda verktyg 2: Azure CLI

1. Hämta och installera [Microsoft Azure kl](/cli/azure/install-azure-cli). Versioner är tillgängliga för Windows, macOS och olika distributioner i Linux.
2. Skapa en PowerShell-fil (fil namns tillägget. ps1), kopiera i följande kod och spara den lokalt.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Redigera filen om du vill använda följande parameter värden. Ange datum i UTC-format, t. ex. 2020-04-01T00:00:00Z.

    - konto namn – namnet på ditt Azure Storage-konto.
    - konto nyckel – din Azure Storage-kontoinformation.
    - start datum – behörighetens start datum för VHD-åtkomst. Ange ett datum en dag före det aktuella datumet.
    - förfallo Datum-datum – behörighetens förfallo datum för VHD-åtkomst. Ange ett datum minst tre veckor efter det aktuella datumet.

    Här är ett exempel på korrekt parameter värden (när detta skrivs):

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Spara ändringarna.
2. Använd någon av följande metoder för att köra det här skriptet med administratörs behörighet för att skapa en SAS-anslutningssträng för åtkomst på behållare nivå:

    - Kör skriptet från-konsolen. I Windows högerklickar du på skriptet och väljer **Kör som administratör**.
    - Kör skriptet från en PowerShell-skriptfil som [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Den här skärmen visar hur en SAS-anslutningssträng skapas i den här redigeraren:

    [![skapa en SAS-anslutningssträng i PowerShell-redigeraren](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Kopiera SAS-anslutningssträngen och spara den i en textfil på en säker plats. Redigera den här strängen för att lägga till information om VHD-platsen för att skapa den sista SAS-URI: n.
7. I Azure Portal går du till blob-lagringen som innehåller den virtuella hård disk som är associerad med den nya URI: n.
8. Kopiera URL: en för BLOB service-slutpunkten:

    ![Kopierar URL: en för BLOB service-slutpunkten.](media/vm/create-sas-uri-blob-endpoint.png)

9. Redigera text filen med SAS-anslutningssträngen från steg 6. Skapa hela SAS-URI: n med följande format:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Verifiera SAS-URI: n

Kontrol lera SAS-URI: n innan du publicerar den på Partner Center för att undvika eventuella problem som rör SAS URI post sändning av begäran. Den här processen är valfri men rekommenderas.

- URI: n innehåller fil namnet för VHD-avbildningen, inklusive fil namns tillägget `.vhd` .
- `Sp=rl` visas nära mitten av din URI. Den här strängen visar Läs-och list åtkomst har angetts.
- När `sr=c` Detta visas innebär det att åtkomst till container nivå har angetts.
- Kopiera och klistra in URI: n i en webbläsare för att testa bloben (du kan avbryta åtgärden innan nedladdningen är klar).

## <a name="next-steps"></a>Nästa steg

- Om du stöter på problem, se [fel meddelanden för virtuella datorer](azure-vm-sas-failure-messages.md).
- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Skapa ett erbjudande för virtuell dator på Azure Marketplace](azure-vm-create.md)