---
title: Så här lägger du till en ny | Microsoft Docs
description: How-To för att lägga till en ny uppdatering i Enhetsuppdatering för IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ecbc76651f09a9b4f2bde01c733cace5037f5fd4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738836"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Lägga till en uppdatering i Enhetsuppdatering för IoT Hub
Lär dig hur du lägger till en ny uppdatering i Enhetsuppdatering för IoT Hub.

## <a name="prerequisites"></a>Förutsättningar

* [Åtkomst till en IoT Hub med Enhetsuppdatering för IoT Hub aktiverat](create-device-update-account.md). 
* En IoT-enhet (eller simulator) som etablerats för enhetsuppdatering inom IoT Hub.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) eller senare (inklusive Linux-, macOS- och Windows-installationer)
* Webbläsare som stöds:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Vissa data som skickas till den här tjänsten kan bearbetas i en region utanför den region som instansen skapades i.

## <a name="obtain-an-update-for-your-devices"></a>Hämta en uppdatering för dina enheter

Nu när [enhetsuppdateringen har ställts](create-device-update-account.md)in är du redo att uppdatera dina enheter. Därefter behöver du de faktiska uppdateringsfiler som du ska distribuera till dessa enheter.

Om du har köpt enheter från en OEM- eller lösningsintegrerare tillhandahåller den organisationen troligen uppdateringsfiler åt dig, utan att du behöver skapa uppdateringarna. Kontakta OEM- eller lösningsintegreraren för att ta reda på hur de gör uppdateringar tillgängliga.

Om din organisation redan skapar programvara för de enheter som du använder, kommer samma grupp att vara den som skapar uppdateringarna för den programvaran. När du skapar en uppdatering som ska distribueras med enhetsuppdatering för IoT Hub, börjar du med antingen den avbildningsbaserade eller [paketbaserade metoden](understand-device-update.md#support-for-a-wide-range-of-update-artifacts) beroende på ditt scenario. Obs! Om du vill skapa egna uppdateringar men precis har börjat är GitHub ett utmärkt alternativ för att hantera din utveckling. Du kan lagra och hantera källkoden och göra kontinuerlig integrering (CI) och kontinuerlig distribution (CD) med hjälp [av GitHub Actions](https://docs.github.com/en/actions/guides/about-continuous-integration).

## <a name="create-a-device-update-import-manifest"></a>Skapa ett importmanifest för enhetsuppdatering

Om du inte redan har gjort det måste du bekanta dig med de grundläggande [importbegreppen](import-concepts.md).

1. Kontrollera att dina uppdateringsfiler finns i en katalog som är tillgänglig från PowerShell.

2. Skapa en textfil med **namnet AduUpdate.psm1** i katalogen där uppdateringsavbildningsfilen eller APT-manifestfilen finns. Öppna sedan PowerShell-cmdleten [AduUpdate.psm1,](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) kopiera innehållet till textfilen och spara sedan textfilen.

3. I PowerShell navigerar du till katalogen där du skapade din PowerShell-cmdlet från steg 2. Använd alternativet Kopiera nedan och klistra sedan in i PowerShell för att köra kommandona:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Kör följande kommandon genom att ersätta exempelparametervärdena för att generera ett importmanifest, en JSON-fil som beskriver uppdateringen:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Här är några exempelvärden för parametrarna ovan som en snabbreferens. Du kan också visa det fullständiga [importmanifestschemat](import-schema.md) för mer information.

    | Parameter | Beskrivning |
    | --------- | ----------- |
    | deviceManufacturer | Enhetens tillverkare som uppdateringen är kompatibel med, till exempel Contoso. Måste matcha _tillverkarens_ [enhetsegenskap](./device-update-plug-and-play.md#device-properties).
    | deviceModel | Modellen för enheten som uppdateringen är kompatibel med, till exempel popup-meddelande. Måste matcha [modellenhetsegenskapen](./device-update-plug-and-play.md#device-properties). 
    | updateProvider | Entitet som skapar eller är direkt ansvarig för uppdateringen. Det är ofta ett företagsnamn.
    | updateName | Identifierare för en klass med uppdateringar. Klassen kan vara vad du än väljer. Det är ofta ett enhets- eller modellnamn.
    | updateVersion | Versionsnummer som särskiljer den här uppdateringen från andra som har samma provider och namn. Matchar inte en version av en enskild programvarukomponent på enheten (men kan det om du väljer).
    | updateType | <ul><li>Ange `microsoft/swupdate:1` för avbildningsuppdatering</li><li>Ange `microsoft/apt:1` för paketuppdatering</li></ul>
    | installedCriteria | <ul><li>Ange värdet för SWVersion för `microsoft/swupdate:1` uppdateringstyp</li><li>Ange name-version , _där namnet_ är namnet på APT-manifestet _och versionen_ är versionen av **APT-manifestet.** Till exempel contoso-iot-edge-1.0.0.0.
    | updateFilePath(s) | Sökväg till uppdateringsfilerna på datorn


## <a name="review-the-generated-import-manifest"></a>Granska det genererade importmanifestet

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

## <a name="import-an-update"></a>Importera en uppdatering

> [!NOTE]
> Anvisningarna nedan visar hur du importerar en uppdatering via Azure Portal användargränssnitt. Du kan också använda [enhetsuppdateringen för att IoT Hub API:er](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) för att importera en uppdatering. 

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din IoT Hub med Enhetsuppdatering.

2. Till vänster på sidan väljer du "Enhetsuppdateringar" under "Automatiska Enhetshantering".

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importera uppdateringar" lightbox="media/import-update/import-updates-3.png":::

3. Du ser flera flikar överst på skärmen. Välj fliken Uppdateringar.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Uppdateringar" lightbox="media/import-update/updates-tab.png":::

4. Välj "+ Importera ny uppdatering" under rubriken "Redo att distribuera".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importera ny uppdatering" lightbox="media/import-update/import-new-update-2.png":::

5. Välj mappikonen eller textrutan under "Välj en importmanifestfil". En dialogruta för filväljaren visas. Välj det importmanifest som du skapade tidigare med Hjälp av PowerShell-cmdleten. Välj sedan mappikonen eller textrutan under "Välj en eller flera uppdateringsfiler". En dialogruta för filväljaren visas. Välj dina uppdateringsfiler.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Välj Uppdatera filer" lightbox="media/import-update/select-update-files.png":::

6. Välj mappikonen eller textrutan under "Välj en lagringscontainer". Välj sedan lämpligt lagringskonto. Lagringscontainern används för att mellanlagring av uppdateringsfilerna tillfälligt.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Lagringskonto" lightbox="media/import-update/storage-account.png":::

7. Om du redan har skapat en container kan du återanvända den. (Annars väljer du "+ Container" för att skapa en ny lagringscontainer för uppdateringar.).  Välj den container som du vill använda och klicka på "Välj".

   :::image type="content" source="media/import-update/container.png" alt-text="Välj container" lightbox="media/import-update/container.png":::

8. Välj "Skicka" för att starta importen.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publicera uppdatering" lightbox="media/import-update/publish-update.png":::

9. Importen börjar och skärmen växlar till avsnittet "Importhistorik". Välj Uppdatera för att visa förloppet tills importen har slutförts (beroende på uppdateringens storlek kan det ta några minuter men kan ta längre tid).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Uppdatera importsekvensering" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. När kolumnen Status visar att importen har lyckats väljer du rubriken "Redo att distribuera". Nu bör du se din importerade uppdatering i listan.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Jobbstatus" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Nästa steg

[Skapa grupper](create-update-group.md)

[Läs mer om importbegrepp](import-concepts.md)