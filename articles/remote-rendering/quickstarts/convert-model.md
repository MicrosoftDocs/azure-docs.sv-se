---
title: Konvertera en modell
description: Snabb start som visar konverterings stegen för en anpassad modell.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: c9b5d525954e7f0742cd13fe4d64a73df64ea854
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594475"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Snabbstart: Konvertera en modell för rendering

I [snabb start: återge en modell med Unit](render-model.md), har du lärt dig hur du använder Unity-exempelprojektet för att rendera en inbyggd modell. Den här guiden visar hur du konverterar egna modeller.

Du lär dig följande:

> [!div class="checklist"]
>
> * Konfigurera ett Azure Blob Storage-konto för indata och utdata
> * Ladda upp och konvertera en 3D-modell för användning med Azure Remote rendering
> * Ta med den konverterade 3D-modellen i ett program för åter givning

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: rendera en modell med Unity](render-model.md)
* För konverteringen med hjälp av PowerShell-skriptet: installera Azure PowerShell [(dokumentation)](/powershell/azure/)
  * Öppna en PowerShell med administratörs behörighet
  * Fungerar `Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Översikt

Åter givningen på servern kan inte arbeta direkt med käll modell format som FBX eller GLTF. I stället måste modellen vara i ett eget binärt format.
Konverterings tjänsten använder modeller från Azure Blob Storage och skriver konverterade modeller tillbaka till en angiven Azure Blob Storage-behållare.

Du behöver:

* En Azure-prenumeration
* Ett ' StorageV2 '-konto i din prenumeration
* En Blob Storage-behållare för din indata modell
* En Blob Storage-behållare för dina utmatnings data
* En modell som ska konverteras finns i [exempel modeller](../samples/sample-model.md)
  * Se listan över [käll format som stöds](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Om du vill använda exempel konverterings skriptet ser du till att förbereda en mapp som innehåller modellen och alla externa beroenden (som externa texturer eller geometrier)

## <a name="azure-setup"></a>Azure-installation

Om du inte har något konto ännu går du till [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/) , klickar på alternativet kostnads fritt konto och följer anvisningarna.

När du har ett Azure-konto går du till [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home) .

### <a name="storage-account-creation"></a>Skapa lagrings konto

För att skapa Blob Storage behöver du först ett lagrings konto.
Om du vill skapa en klickar du på knappen "skapa en resurs":

![Azure – Lägg till resurs](media/azure-add-a-resource.png)

Från den nya skärmen väljer du **lagring** till vänster och sedan **lagrings konto – BLOB, fil, tabell, kö** från nästa kolumn:

![Azure – Lägg till lagring](media/azure-add-storage.png)

Genom att klicka på den här knappen visas följande skärm med lagrings egenskaper som ska fyllas i:

![Azure-installation](media/azure-setup1.png)

Fyll i formuläret på följande sätt:

* Skapa en ny resurs grupp från länken nedanför List rutan och ge den namnet **ARR_Tutorial**
* För **lagrings konto namnet** anger du ett unikt namn här. **Det här namnet måste vara globalt unikt**, annars visas en uppfattande meddelande som informerar att namnet redan är upptaget. I omfånget för den här snabb starten namnger vi IT- **arrtutorialstorage**. Därefter måste du ersätta den med ditt namn för valfri förekomst i den här snabb starten.
* Välj en **plats** nära dig. Vi rekommenderar att du använder samma plats som används för att ställa in renderingen i den andra snabb starten.
* **Prestanda** har angetts till "standard"
* **Konto typ** har angetts till "StorageV2 (generell användning v2)"
* **Replikering** har angetts till "Read-Access Geo-redundant Storage (RA-GRS)"
* **Åtkomst nivån** har angetts till "Het"

Ingen av egenskaperna i andra flikar måste ändras, så du kan fortsätta med **"granska + skapa"** och sedan följa stegen för att slutföra installationen.

Webbplatsen informerar nu om förloppet för distributionen och rapporterna att distributionen är klar. Klicka på knappen **gå till resurs** för att få nästa steg:

![Azure Storage har skapats](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Generering av blob-lagring

Härnäst behöver vi två BLOB-behållare, en för indata och en för utdata.

Från knappen **gå till resurs** ovan kommer du till en sida med en panel till vänster som innehåller en List meny. I listan under kategorin **"BLOB service"** klickar du på knappen **behållare** :

![Azure – Lägg till behållare](./media/azure-add-containers.png)

Tryck på **"+ container"** -knappen för att skapa Blob storage-behållaren för **indata** .
Använd följande inställningar när du skapar den:
  
* Namn = arrinput
* Offentlig åtkomst nivå = privat

När behållaren har skapats klickar du på **+ container** igen och upprepar de här inställningarna för behållaren **utdata** :

* Namn = arroutput
* Offentlig åtkomst nivå = privat

Du bör nu ha två Blob Storage-behållare:

![Blob Storage installation](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Kör konverteringen

Det finns tre olika sätt att utlösa en modell konvertering:

### <a name="1-conversion-via-the-arrt-tool"></a>1. Conversion via verktyget ARRT

Det finns ett [UI-baserat verktyg som heter ARRT](./../samples/azure-remote-rendering-asset-tool.md) för att starta konverteringar och interagera med det resulterande resultatet.
![ARRT](./../samples/media/azure-remote-rendering-asset-tool.png "ARRT skärm bild")

### <a name="2-conversion-via-a-powershell-script"></a>2. Conversion via ett PowerShell-skript

För att göra det enklare att anropa till gångs konverterings tjänsten tillhandahåller vi ett verktygs skript. Den finns i mappen *skript* och kallas **Conversion.ps1**.

I synnerhet detta skript

1. överför alla filer i en specifik katalog från den lokala disken till behållaren för indata-lagring
1. anropar den [till gångs konverterings REST API](../how-tos/conversion/conversion-rest-api.md), som hämtar data från behållaren för inkommande lagring och startar en konvertering som returnerar ett konverterings-ID
1. Avsök konverterings status-API: et med det hämtade konverterings-ID: t tills konverterings processen avslutas med lyckat eller misslyckat
1. hämtar en länk till den konverterade till gången i utgående lagring

Skriptet läser konfigurationen från filen *Scripts\arrconfig.jspå*. Öppna JSON-filen i en text redigerare.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

Konfigurationen i **accountSettings** -gruppen (konto-ID och nyckel) bör fyllas i analogt med autentiseringsuppgifterna i avsnittet [rendera en modell med enhets snabb start](render-model.md).

I gruppen **assetConversionSettings** ser du till att ändra **resourceGroup**, **blobInputContainerName** och **blobOutputContainerName** som visas ovan.
Observera att värdet för **arrtutorialstorage** måste ersättas med det unika namn som du valde när du skapade lagrings kontot.

Ändra **localAssetDirectoryPath** så att den pekar på katalogen på disken, som innehåller den modell som du vill konvertera. Var noga med att kringgå omvända snedstreck (" \\ ") i sökvägen med dubbla omvända snedstreck (" \\ \\ ").

Alla data från sökvägen som anges i **localAssetDirectoryPath** överförs till **blobInputContainerName** -BLOB-behållaren under en under Sök väg som anges av **inputFolderPath**. Så i exempel konfigurationen ovan överförs innehållet i katalogen "D: \\ tmp \\ robot" till BLOB-behållaren "arrinput" för lagrings kontot "arrtutorialstorage" under sökvägen "robotConversion". Redan befintliga filer kommer att skrivas över.

Ändra **inputAssetPath** till sökvägen till den modell som ska konverteras – sökvägen är relativ till localAssetDirectoryPath. Använd "/" i stället för " \\ " som Sök vägs avgränsare. Så för en robot. FBX-fil, som finns direkt i "D: \\ tmp \\ robot", använder du "robot. FBX".

När modellen har konverterats kommer den att skrivas tillbaka till den lagrings behållare som anges av **blobOutputContainerName**. En under Sök väg kan anges genom att tillhandahålla den valfria **outputFolderPath**. I exemplet ovan kommer den resulterande "robot. arrAsset" kopieras till BLOB-behållaren för utgående data under "konverterad/robot".

Konfigurations inställningen **outputAssetFileName** bestämmer namnet på den konverterade till gången – parametern är valfri och utdata-filnamn kommer att härledas från indatafilens namn annars.

Öppna en PowerShell, kontrol lera att du har installerat *Azure PowerShell* som anges i [kraven](#prerequisites). Logga sedan in på prenumerationen med följande kommando och följ anvisningarna på skärmen:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Om din organisation har mer än en prenumeration kan du behöva ange SubscriptionId-och klient argument. Mer information finns i [dokumentationen för Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Ändra till `azure-remote-rendering\Scripts` katalogen och kör konverterings skriptet:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Du bör se något som liknar detta: ![Conversion.ps1](./media/successful-conversion.png)

### <a name="3-conversion-via-api-calls"></a>3. konvertering via API-anrop

Både C#-och C++-API: et tillhandahåller en start punkt för att interagera med tjänsten:
* [C# RemoteRenderingClient. StartAssetConversionAsync ()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.startassetconversionasync)
* [C++ RemoteRenderingClient:: StartAssetConversionAsync ()](/cpp/api/remote-rendering/remoterenderingclient#startassetconversionasync)


## <a name="insert-new-model-into-quickstart-sample-app"></a>Infoga ny modell i exempel appen för snabb start

Konverterings skriptet genererar en URL för *signaturer för delad åtkomst (SAS)* för den konverterade modellen. Nu kan du kopiera den här URI: n som **modell namn** till exempel appen snabb start (se [snabb start: återge en modell med Unity](render-model.md)).

![Ersätt modell i Unity](./media/replace-model-in-unity.png)

 Exemplet bör nu läsa in och återge din anpassade modell!

## <a name="optional-re-creating-a-sas-uri"></a>Valfritt: skapa en SAS-URI igen

SAS-URI: n som skapas av konverterings skriptet är bara giltig i 24 timmar. När den har gått ut behöver du dock inte konvertera modellen igen. I stället kan du skapa en ny SAS i portalen enligt beskrivningen i nästa steg:

1. Gå till [Azure Portal](https://www.portal.azure.com)
1. Klicka på din **lagrings konto** resurs: ![ skärm bild som markerar den valda lagrings konto resursen.](./media/portal-storage-accounts.png)
1. I följande skärm klickar du på **lagrings Utforskaren** på den vänstra panelen och letar reda på din utmatnings modell (*. arrAsset* -fil) i *arroutput* Blob storage-behållaren. Högerklicka på filen och välj **Hämta signatur för delad åtkomst** från snabb menyn: ![ åtkomst till signatur](./media/portal-storage-explorer.png)
1. En ny skärm öppnas där du kan välja ett utgångs datum. Tryck på **skapa** och kopiera URI: n som visas i nästa dialog ruta. Denna nya URI ersätter den temporära URI som skriptet skapade.

## <a name="next-steps"></a>Nästa steg

Nu när du vet grunderna kan du få en titt på våra självstudier för att få mer djupgående kunskap.

Om du vill lära dig mer om modell konverteringen kan du ta [en titt på modell konverterings REST API](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Självstudie: Visa fjärranslutna modeller](../tutorials/unity/view-remote-models/view-remote-models.md)