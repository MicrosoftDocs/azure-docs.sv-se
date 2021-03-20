---
title: Konfigurera fil uppladdning till IoT Hub med hjälp av Azure CLI | Microsoft Docs
description: Så här konfigurerar du fil överföringar till Azure IoT Hub med plattforms oberoende Azure CLI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: 4dbda13ffe04e0a4214b24ccaca2b8103a39b9f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536070"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurera IoT Hub fil överföringar med Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Om du vill [Ladda upp filer från en enhet](iot-hub-devguide-file-upload.md)måste du först associera ett Azure Storage-konto med IoT-hubben. Du kan använda ett befintligt lagrings konto eller skapa ett nytt.

För att göra den här självstudien behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure CLI](/cli/azure/install-azure-cli).

* En Azure IoT-hubb. Om du inte har en IoT-hubb kan du använda [ `az iot hub create` kommandot](/cli/azure/iot/hub#az-iot-hub-create) för att skapa en eller [skapa en IoT-hubb med portalen](iot-hub-create-through-portal.md).

* Ett Azure Storage-konto. Om du inte har ett Azure Storage konto kan du använda Azure CLI för att skapa ett. Mer information finns i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välj din prenumeration.

1. I kommandotolken kör du [inloggningskommandot](/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

2. Om du har flera Azure-prenumerationer får du åtkomst till alla Azure-konton som är associerade med dina autentiseringsuppgifter när du loggar in på Azure. Använd följande [-kommando för att lista Azure-konton](/cli/azure/account) som du kan använda:

    ```azurecli
    az account list
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta information om lagrings kontot

Följande steg förutsätter att du har skapat ditt lagrings konto med distributions modellen **Resource Manager** och inte den **klassiska** distributions modellen.

Om du vill konfigurera fil överföringar från dina enheter behöver du anslutnings strängen för ett Azure Storage-konto. Lagrings kontot måste finnas i samma prenumeration som din IoT Hub. Du behöver också namnet på en BLOB-behållare i lagrings kontot. Använd följande kommando för att hämta dina lagrings konto nycklar:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Anteckna **ConnectionString** -värdet. Du behöver den i följande steg.

Du kan antingen använda en befintlig BLOB-behållare för dina fil överföringar eller skapa en ny:

* Om du vill visa en lista över befintliga BLOB-behållare i ditt lagrings konto använder du följande kommando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Använd följande kommando för att skapa en BLOB-behållare i ditt lagrings konto:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Fil uppladdning

Nu kan du konfigurera din IoT Hub så att du kan [Ladda upp filer till IoT Hub](iot-hub-devguide-file-upload.md) med hjälp av informationen om lagrings kontot.

Konfigurationen kräver följande värden:

* **Lagrings behållare**: en BLOB-behållare i ett Azure Storage-konto i din aktuella Azure-prenumeration som ska associeras med IoT-hubben. Du har hämtat den nödvändiga lagrings konto informationen i föregående avsnitt. IoT Hub skapar automatiskt SAS-URI: er med Skriv behörighet till den här BLOB-behållaren för enheter som ska användas när de laddar upp filer.

* **Ta emot meddelanden om överförda filer**: Aktivera eller inaktivera meddelanden om fil uppladdning.

* **SAS-TTL**: den här inställningen är TTL-värdet för de SAS-URI: er som returnerades till enheten med IoT Hub. Ange en timme som standard.

* **Inställningar för fil meddelanden standard TTL: TTL-värde** för fil överföring innan det upphör att gälla. Ange en dag som standard.

* **Maximalt antal leveranser för fil meddelanden**: antalet gånger som IoT Hub försöker leverera ett meddelande om fil överföring. Ange till 10 som standard.

Använd följande Azure CLI-kommandon för att konfigurera fil överförings inställningarna på din IoT-hubb:

<!--Robinsh this is out of date, add cloud powershell -->

Använd följande i ett bash-gränssnitt:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Du kan granska fil överförings konfigurationen på IoT-hubben med följande kommando:

```azurecli
az iot hub show --name {your iot hub name}
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