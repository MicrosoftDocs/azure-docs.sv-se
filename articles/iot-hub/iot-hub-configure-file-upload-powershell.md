---
title: Använd Azure PowerShell för att konfigurera fil uppladdning | Microsoft Docs
description: Använda Azure PowerShell-cmdletar för att konfigurera IoT Hub för att aktivera fil överföringar från anslutna enheter. Innehåller information om hur du konfigurerar målets Azure Storage-konto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: e2b106e64aed08b0586575d4d77602329454a673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536019"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurera IoT Hub fil överföringar med PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Om du vill använda [fil uppladdnings funktionen i IoT Hub](iot-hub-devguide-file-upload.md)måste du först associera ett Azure Storage-konto med IoT Hub. Du kan använda ett befintligt lagrings konto eller skapa ett nytt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att göra den här självstudien behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure PowerShell-cmdletar](/powershell/azure/install-Az-ps).

* En Azure IoT-hubb. Om du inte har en IoT-hubb kan du använda [cmdleten New-AzIoTHub](/powershell/module/az.iothub/new-aziothub) för att skapa en eller använda portalen för att [skapa en IoT-hubb](iot-hub-create-through-portal.md).

* Ett Azure-lagringskonto. Om du inte har ett Azure Storage-konto kan du använda [Azure Storage PowerShell-cmdletar](/powershell/module/az.storage/) för att skapa en eller använda portalen för att [skapa ett lagrings konto](../storage/common/storage-account-create.md)

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välj din prenumeration.

1. Kör cmdleten **Connect-AzAccount** i PowerShell-prompten:

    ```powershell
    Connect-AzAccount
    ```

2. Om du har flera Azure-prenumerationer ger du till gång till alla Azure-prenumerationer som är kopplade till dina autentiseringsuppgifter genom att logga in på Azure. Använd följande kommando för att lista de Azure-prenumerationer som du kan använda:

    ```powershell
    Get-AzSubscription
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandona för att hantera IoT-hubben. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta information om lagrings kontot

Följande steg förutsätter att du har skapat ditt lagrings konto med distributions modellen **Resource Manager** och inte den **klassiska** distributions modellen.

Om du vill konfigurera fil överföringar från dina enheter behöver du anslutnings strängen för ett Azure Storage-konto. Lagrings kontot måste finnas i samma prenumeration som din IoT Hub. Du behöver också namnet på en BLOB-behållare i lagrings kontot. Använd följande kommando för att hämta dina lagrings konto nycklar:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Anteckna värdet för lagrings kontots **KEY1** . Du behöver den i följande steg.

Du kan antingen använda en befintlig BLOB-behållare för dina fil överföringar eller skapa en ny:

* Om du vill visa en lista över befintliga BLOB-behållare i ditt lagrings konto använder du följande kommandon:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Använd följande kommandon för att skapa en BLOB-behållare i ditt lagrings konto:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurera din IoT Hub

Nu kan du konfigurera din IoT Hub för att [Ladda upp filer till IoT Hub](iot-hub-devguide-file-upload.md) med hjälp av informationen om lagrings kontot.

Konfigurationen kräver följande värden:

* **Lagrings behållare**: en BLOB-behållare i ett Azure Storage-konto i din aktuella Azure-prenumeration som ska associeras med IoT-hubben. Du har hämtat den nödvändiga lagrings konto informationen i föregående avsnitt. IoT Hub skapar automatiskt SAS-URI: er med Skriv behörighet till den här BLOB-behållaren för enheter som ska användas när de laddar upp filer.

* **Ta emot meddelanden om överförda filer**: Aktivera eller inaktivera meddelanden om fil uppladdning.

* **SAS-TTL**: den här inställningen är TTL-värdet för de SAS-URI: er som returnerades till enheten med IoT Hub. Ange en timme som standard.

* **Inställningar för fil meddelanden standard TTL: TTL-värde** för fil överföring innan det upphör att gälla. Ange en dag som standard.

* **Maximalt antal leveranser för fil meddelanden**: antalet gånger som IoT Hub försöker leverera ett meddelande om fil överföring. Ange till 10 som standard.

Använd följande PowerShell-cmdlet för att konfigurera fil överförings inställningarna på din IoT-hubb:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>Nästa steg

Mer information om fil överförings funktionerna i IoT Hub finns i [överföra filer från en enhet](iot-hub-devguide-file-upload.md).

Följ dessa länkar om du vill veta mer om hur du hanterar Azure-IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [Övervaka din IoT Hub](monitor-iot-hub.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Skydda din IoT-lösning från grunden](../iot-fundamentals/iot-security-ground-up.md)