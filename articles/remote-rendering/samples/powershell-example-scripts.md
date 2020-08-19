---
title: PowerShell-exempelskript
description: Exempel som visar hur du använder klient delen via PowerShell-skript
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: 07055025eff9ab81c7321624daed9b4a6e993a60
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506519"
---
# <a name="example-powershell-scripts"></a>PowerShell-exempelskript

Azure Remote rendering innehåller följande två REST-API: er:

- [Konverterings REST API](../how-tos/conversion/conversion-rest-api.md)
- [Session REST API](../how-tos/session-rest-api.md)

[Plats exemplen för arr](https://github.com/Azure/azure-remote-rendering) innehåller exempel skript i mappen *skript* för att interagera med REST-API: erna för tjänsten. I den här artikeln beskrivs användningen.

## <a name="prerequisites"></a>Krav

Om du vill köra exempel skripten behöver du en fungerande installation av [Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

1. Installera Azure PowerShell:
    1. Öppna ett PowerShell-fönster med administratörs behörighet.
    1. Fungerar `Install-Module -Name Az -AllowClobber`

1. Om du får problem med att köra skript, se till att [körnings principen](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) är korrekt inställd:
    1. Öppna ett PowerShell-fönster med administratörs behörighet.
    1. Fungerar `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Förbereda ett Azure Storage-konto](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Logga in på din prenumeration som innehåller ditt Azure-konto för fjärrrendering:
    1. Öppna ett PowerShell-fönster.
    1. Kör: `Connect-AzAccount` och följ anvisningarna på skärmen.

    > [!NOTE]
    > Om din organisation har mer än en prenumeration kan du behöva ange SubscriptionId-och klient argument. Mer information finns i [dokumentationen för Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

1. Hämta mappen *skript* från GithHub- [lagringsplatsen för Azure Remote rendering](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Konfigurationsfil

Bredvid `.ps1` filerna finns det en `arrconfig.json` som du måste fylla i:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Se till att du på ett korrekt sätt kan kringgå omvänt snedstreck i LocalAssetDirectoryPath-sökvägen med dubbla omvända snedstreck: " \\ \\ " och Använd snedstreck "/" i alla andra sökvägar som InputFolderPath och inputAssetPath.

> [!CAUTION]
> Valfria värden måste fyllas i eller så måste du ta bort nyckeln och värdet helt. Om du till exempel inte använder  `"outputAssetFileName"` parametern måste du ta bort hela raden i `arrconfig.json` .

### <a name="accountsettings"></a>accountSettings

Mer `arrAccountId` `arrAccountKey` information finns i [skapa ett konto för Azure Remote rendering](../how-tos/create-an-account.md).
`region`Se [listan över tillgängliga regioner](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Den här strukturen måste fyllas i om du vill köra **RenderingSession.ps1**:

- **vmSize:** Väljer storlek på den virtuella datorn. Välj [*standard*](../reference/vm-sizes.md) eller [*Premium*](../reference/vm-sizes.md). Stäng av åter givnings sessioner när du inte behöver dem längre.
- **maxLeaseTime:** Den varaktighet som du vill att den virtuella datorn ska lånas ut för. Det kommer att stängas av när lånet upphör att gälla. Låne tiden kan utökas senare (se nedan).

### <a name="assetconversionsettings"></a>assetConversionSettings

Den här strukturen måste fyllas i om du vill köra **Conversion.ps1**.

Mer information finns i [förbereda ett Azure Storage-konto](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Skript: RenderingSession.ps1

Det här skriptet används för att skapa, fråga och stoppa åter givning av sessioner.

> [!IMPORTANT]
> Se till att du har fyllt i avsnitten *accountSettings* och *renderingSessionSettings* i arrconfig.jspå.

### <a name="create-a-rendering-session"></a>Skapa en åter givnings session

Normal användning med en fullständigt ifylld arrconfig.jspå:

```PowerShell
.\RenderingSession.ps1
```

Skriptet anropar [sessions hanterings REST API](../how-tos/session-rest-api.md) för att skapa en rendering virtuell dator med de angivna inställningarna. Vid lyckad hämtning kommer det att hämta *SessionID*. Sedan avsöks sessionens egenskaper tills sessionen är klar eller ett fel har uppstått.

Så här använder du en **alternativ konfigurations** fil:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Du kan **åsidosätta enskilda inställningar** från konfigurations filen:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Om du bara vill **starta en session utan avsökning**kan du använda:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

*SessionID* som skriptet hämtar måste skickas till de flesta andra session-kommandon.

### <a name="retrieve-session-properties"></a>Hämta egenskaper för session

Kör följande om du vill hämta en sessions egenskaper:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Används `-Poll` för att vänta tills sessionen är *klar* eller ett fel har inträffat.

### <a name="list-active-sessions"></a>Visa lista över aktiva sessioner

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Stoppa en session

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Ändra egenskaper för session

För tillfället stöder vi bara ändring av maxLeaseTime för en session.

> [!NOTE]
> Låne tiden räknas alltid från den tidpunkt då den virtuella sessionen skapades. Om du vill utöka session-lånet med en annan timme ökar du *maxLeaseTime* med en timme.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Skript: Conversion.ps1

Det här skriptet används för att konvertera inmatade modeller till Azure Remote rendering Specific runtime-formatet.

> [!IMPORTANT]
> Se till att du har fyllt i avsnitten *accountSettings* och *assetConversionSettings* i arrconfig.jspå.

Skriptet visar de två alternativen för att använda lagrings konton med tjänsten:

- Lagrings konto som är kopplat till Azure Remote rendering-kontot
- Ge åtkomst till lagring via signaturer för delad åtkomst (SAS)

### <a name="linked-storage-account"></a>Länkat lagrings konto

När du har fyllt i arrconfig.jspå och länkat ett lagrings konto kan du använda följande kommando. Att länka ditt lagrings konto beskrivs i [skapa ett konto](../how-tos/create-an-account.md#link-storage-accounts).

Att använda ett länkat lagrings konto är det bästa sättet att använda konverterings tjänsten eftersom det inte finns några behov av att generera signaturer för delad åtkomst.

```PowerShell
.\Conversion.ps1
```

1. Ladda upp alla filer som finns i `assetConversionSettings.modelLocation` till BLOB-behållaren för inflöde under den angivna `inputFolderPath` ..
1. Anropa modell konverterings [REST API](../how-tos/conversion/conversion-rest-api.md) för att starta [modell konverteringen](../how-tos/conversion/model-conversion.md)
1. Avsök konverterings statusen tills konverteringen lyckades eller misslyckades.
1. Information om den konverterade filens plats (lagrings konto, utdatafil, fil Sök väg i behållaren).

### <a name="access-to-storage-via-shared-access-signatures"></a>Åtkomst till lagring via signaturer för delad åtkomst

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Detta kommer att:

1. Ladda upp den lokala filen från `assetConversionSettings.localAssetDirectoryPath` till BLOB-behållaren för indata.
1. Generera en SAS-URI för behållaren för indatamängd.
1. Generera en SAS-URI för behållaren för utdata.
1. Anropa modell konverterings [REST API](../how-tos/conversion/conversion-rest-api.md) för att starta [modell konverteringen](../how-tos/conversion/model-conversion.md).
1. Avsök konverterings statusen tills konverteringen lyckades eller misslyckades.
1. Information om den konverterade filens plats (lagrings konto, utdatafil, fil Sök väg i behållaren).
1. Utdata från en SAS-URI till den konverterade modellen i BLOB-behållaren utgående.

### <a name="additional-command-line-options"></a>Ytterligare kommando rads alternativ

Så här använder du en **alternativ konfigurations** fil:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Om du bara vill **Starta modell konvertering utan avsökning**kan du använda:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Du kan **åsidosätta enskilda inställningar** från konfigurations filen med följande kommando rads växlar:

* **ID:** ConversionId som används med GetConversionStatus
* **ArrAccountId:** ArrAccountId för accountSettings
* **ArrAccountKey:** åsidosättande för ArrAccountKey för accountSettings
* **Region:** åsidosättande för regionen accountSettings
* **ResourceGroup:** åsidosättande för ResourceGroup för assetConversionSettings
* **StorageAccountName:** åsidosättande för StorageAccountName för assetConversionSettings
* **BlobInputContainerName:** åsidosättande för BlobInputContainer för assetConversionSettings
* **LocalAssetDirectoryPath:** åsidosättande för LocalAssetDirectoryPath för assetConversionSettings
* **InputAssetPath:** åsidosättande för InputAssetPath för assetConversionSettings
* **BlobOutputContainerName:** åsidosättande för BlobOutputContainerName för assetConversionSettings
* **OutputFolderPath:** åsidosättande för OutputFolderPath för assetConversionSettings
* **OutputAssetFileName:** åsidosättande för OutputAssetFileName för assetConversionSettings

Du kan till exempel kombinera ett antal alternativ så här:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Kör de enskilda konverterings stegen

Om du vill köra enskilda steg i processen kan du använda:

Ladda bara upp data från den aktuella LocalAssetDirectoryPath.

```PowerShell
.\Conversion.ps1 -Upload
```

Starta bara konverterings processen för en modell som redan har laddats upp till Blob Storage (kör inte uppladdning, Avsök inte konverterings status) skriptet returnerar en *conversionId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Du kan också hämta konverterings statusen för den här konverteringen med:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Används `-Poll` för att vänta tills konverteringen är genomförd eller ett fel uppstod.

## <a name="next-steps"></a>Nästa steg

- [Snabb start: rendera en modell med Unity](../quickstarts/render-model.md)
- [Snabb start: konvertera en modell för åter givning](../quickstarts/convert-model.md)
- [Modell konvertering](../how-tos/conversion/model-conversion.md)
