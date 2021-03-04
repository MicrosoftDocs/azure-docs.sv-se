---
title: Så här importerar du en ny uppdatering | Microsoft Docs
description: How-Tos guide för att importera en ny uppdatering till IoT Hub enhets uppdatering för IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: c83221743e0566d783c38c40aaf92111a0cd80f7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030740"
---
# <a name="import-new-update"></a>Importera ny uppdatering
Lär dig hur du importerar en ny uppdatering till enhets uppdatering för IoT Hub.

## <a name="prerequisites"></a>Förutsättningar

* [Åtkomst till en IoT Hub med enhets uppdatering för IoT Hub aktive rad](create-device-update-account.md). Vi rekommenderar att du använder en S1-nivå (standard) eller högre för IoT Hub. 
* En IoT-enhet (eller Simulator) etablerad för enhets uppdatering i IoT Hub.
   * Om du använder en riktig enhet behöver du en uppdaterings avbildnings fil för avbildnings uppdatering eller [apt manifest fil](device-update-apt-manifest.md) för paket uppdatering.
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) eller senare.
* Webbläsare som stöds:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Vissa data som skickas till den här tjänsten kan bearbetas i en region utanför den region som den här instansen skapades i.

## <a name="create-device-update-import-manifest"></a>Skapa import manifest för enhets uppdatering

1. Se till att din uppdaterings avbildnings fil eller APT manifest fil finns i en katalog som är tillgänglig från PowerShell.

2. Klona [enhets uppdatering för IoT Hub-lagringsplatsen](https://github.com/azure/iot-hub-device-update)eller ladda ned den som en. zip-fil till en plats som är tillgänglig från PowerShell (när zip-filen har laddats ned högerklickar du på `Properties`  >  `General` fliken > check `Unblock` i `Security` avsnittet för att undvika PowerShell-Säkerhetsvarnings meddelanden).

3. I PowerShell navigerar du till `tools/AduCmdlets` katalog och kör:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Kör följande kommandon genom att ersätta exempel parameter värden för att generera ett import manifest, en JSON-fil som beskriver uppdateringen:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Här är några exempel värden för en snabb referens för ovanstående parametrar. Du kan också visa det fullständiga [import Manifestets schema](import-schema.md) för mer information.

    | Parameter | Beskrivning |
    | --------- | ----------- |
    | deviceManufacturer | Enhetens tillverkare som uppdateringen är kompatibel med, till exempel contoso
    | deviceModel | Enhets modellen som uppdateringen är kompatibel med, till exempel toaster
    | updateProvider | Leverantörs del av uppdaterings identitet, till exempel Fabrikam
    | updateName | Namn del av uppdaterings identitet, till exempel ImageUpdate
    | updateVersion | Uppdaterings version, till exempel 2,0
    | Uppdateringstyp | <ul><li>Ange `microsoft/swupdate:1` för avbildnings uppdatering</li><li>Ange `microsoft/apt:1` för paket uppdatering</li></ul>
    | installedCriteria | <ul><li>Ange värdet för SWVersion för `microsoft/swupdate:1` uppdaterings typ</li><li>Ange Rekommenderat värde för `microsoft/apt:1` uppdaterings typen.
    | updateFilePath (s) | Sökväg till uppdaterings filen (-erna) på datorn


## <a name="review-generated-import-manifest"></a>Granska genererat import manifest

Exempel:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Importera uppdatering

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till IoT Hub med enhets uppdatering.

2. Välj "enhets uppdateringar" under "automatisk enhets hantering" på vänster sida på sidan.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importera uppdateringar" lightbox="media/import-update/import-updates-3.png":::

3. Flera flikar visas överst på skärmen. Välj fliken uppdateringar.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Uppdateringar" lightbox="media/import-update/updates-tab.png":::

4. Välj "+ Importera ny uppdatering" under rubriken "klar att distribuera".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importera ny uppdatering" lightbox="media/import-update/import-new-update-2.png":::

5. Välj mappikonen eller text rutan under "Välj en import manifest fil". Du kommer att se en dialog ruta för fil väljaren. Välj det import manifest som du skapade tidigare med PowerShell-cmdleten. Välj sedan mappikonen eller text rutan under "Välj en eller flera uppdateringsfiler". Du kommer att se en dialog ruta för fil väljaren. Välj uppdaterings fil (er).

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Markera uppdateringsfiler" lightbox="media/import-update/select-update-files.png":::

6. Välj mappikonen eller text rutan under "Välj en lagrings behållare". Välj sedan lämpligt lagrings konto. Lagrings behållaren används för att mellanlagra uppdateringsfilerna tillfälligt.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Lagringskonto" lightbox="media/import-update/storage-account.png":::

7. Om du redan har skapat en behållare kan du återanvända den. (Annars väljer du "+ behållare" för att skapa en ny lagrings behållare för uppdateringar.).  Välj den behållare som du vill använda och klicka på Välj.

   :::image type="content" source="media/import-update/container.png" alt-text="Välj behållare" lightbox="media/import-update/container.png":::

8. Välj "Skicka" för att starta import processen.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publicera uppdatering" lightbox="media/import-update/publish-update.png":::

9. Import processen påbörjas och skärmen växlar till avsnittet import historik. Välj "uppdatera" om du vill visa förloppet tills importen är klar (beroende på Uppdateringens storlek kan detta slutföras om några minuter, men det kan ta längre tid).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Uppdatera ordningsföljd för import" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. När kolumnen status visar att importen har slutförts väljer du rubriken "klar att distribuera". Du bör se den importerade uppdateringen i listan nu.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Jobb status" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Nästa steg

[Skapa grupper](create-update-group.md)

[Lär dig mer om att importera begrepp](import-concepts.md)
