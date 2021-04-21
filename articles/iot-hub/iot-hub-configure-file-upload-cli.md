---
title: Konfigurera filuppladdning till IoT Hub azure CLI-| Microsoft Docs
description: Så här konfigurerar du filuppladdningar Azure IoT Hub med hjälp av plattformsoberoende Azure CLI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: ea6ec30ad5f3b1cdbc906cc94cb211295b84e802
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761735"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurera IoT Hub filuppladdningar med Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Om [du vill ladda upp filer från](iot-hub-devguide-file-upload.md)en enhet måste du först associera Azure Storage med din IoT-hubb. Du kan använda ett befintligt lagringskonto eller skapa ett nytt.

För att göra den här självstudien behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure CLI](/cli/azure/install-azure-cli).

* En Azure IoT-hubb. Om du inte har en IoT-hubb [ `az iot hub create` ](/cli/azure/iot/hub#az_iot_hub_create) kan du använda kommandot för att skapa en eller Skapa en [IoT-hubb med hjälp av portalen](iot-hub-create-through-portal.md).

* Ett Azure Storage-konto. Om du inte har ett Azure Storage kan du använda Azure CLI för att skapa ett. Mer information finns i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).

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

## <a name="retrieve-your-storage-account-details"></a>Hämta information om ditt lagringskonto

Följande steg förutsätter att du har skapat ditt lagringskonto **med Resource Manager** distributionsmodellen och inte den **klassiska distributionsmodellen.**

Om du vill konfigurera filuppladdningar från dina enheter behöver du anslutningssträngen för ett Azure Storage-konto. Lagringskontot måste finnas i samma prenumeration som din IoT-hubb. Du behöver också namnet på en blobcontainer i lagringskontot. Använd följande kommando för att hämta dina lagringskontonycklar:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Anteckna **connectionString-värdet.** Du behöver den i följande steg.

Du kan antingen använda en befintlig blobcontainer för dina filuppladdningar eller skapa en ny:

* Om du vill visa en lista över befintliga blobcontainrar i ditt lagringskonto använder du följande kommando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Om du vill skapa en blobcontainer i ditt lagringskonto använder du följande kommando:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Filuppladdning

Nu kan du konfigurera din IoT-hubb så att du kan ladda upp filer till [IoT-hubben](iot-hub-devguide-file-upload.md) med dina lagringskontouppgifter.

Konfigurationen kräver följande värden:

* **Lagringscontainer:** En blobcontainer i ett Azure Storage-konto i din aktuella Azure-prenumeration som ska associeras med din IoT-hubb. Du hämtade nödvändig lagringskontoinformation i föregående avsnitt. IoT Hub genererar automatiskt SAS-URI:er med skrivbehörighet till blobcontainern som enheter kan använda när de laddar upp filer.

* **Ta emot meddelanden om uppladdade filer:** Aktivera eller inaktivera filuppladdningsaviseringar.

* **SAS TTL:** Den här inställningen är time to live för SAS-URI:er som returneras till enheten av IoT Hub. Ange till en timme som standard.

* **Standard-TTL för** filaviseringsinställningar: TTL-värdet för ett meddelande om filuppladdning innan det upphör att gälla. Ange till en dag som standard.

* **Maximalt antal filaviseringar:** Det antal gånger som IoT Hub försöker leverera en filuppladdningsavisering. Ange till 10 som standard.

Använd följande Azure CLI-kommandon för att konfigurera filuppladdningsinställningarna på din IoT-hubb:

<!--Robinsh this is out of date, add cloud powershell -->

I ett bash-gränssnitt använder du:

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

Du kan granska filuppladdningskonfigurationen på din IoT-hubb med hjälp av följande kommando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Nästa steg

Mer information om filuppladdningsfunktionerna i IoT Hub finns i [Ladda upp filer från en enhet.](iot-hub-devguide-file-upload.md)

Följ dessa länkar om du vill veta mer om att hantera Azure IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [Övervaka din IoT-hubb](monitor-iot-hub.md)

Mer information om funktionerna i IoT Hub finns i:

* [IoT Hub utvecklarguide](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Skydda din IoT-lösning från grunden](../iot-fundamentals/iot-security-ground-up.md)